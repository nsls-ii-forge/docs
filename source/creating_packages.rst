.. role:: raw-html(raw)
   :format: html

#################
Creating Packages
#################

Feedstocks for `Azure Pipelines
<https://dev.azure.com/nsls2forge/nsls2forge/_build>`_ may be generated based
on the following sources (listed in the order of preference):

- `conda-forge feedstocks <https://github.com/conda-forge>`_;

- `PyPI packages <https://pypi.org/>`_;

- package recipes from
  `lightsource2-recipes <https://github.com/NSLS-II/lightsource2-recipes/tree/master/recipes-tag>`_.

For a new package, the recipes (in the form of ``meta.yaml`` file) can be
created manually from a generic template for ``meta.yaml`` file and specific
package requirements.

===================
Directory structure
===================

The following directory structure for the packaging project is recommended:

  - ``~/src/nsls-ii-forge/`` -- root directory in which packages will be
    placed. This is only a recommended name. In practice, root directory may
    have arbitrary name and placed in any convenient spot.

  - ``~/src/nsls-ii-forge/event-model-feedstock`` -- the directory with cloned
    **event-model-feedstock** repository.

For each package we will create a temporary directory that may be deleted after
a feedstock is initialized and the feedstock directory that will contain the
feedstock repository:

  - ``~/src/nsls-ii-forge/<package_name>`` -- a temporary directory with the
    package's recipe. The directory is created by **conda skeleton** or
    manually. The directory name has to match the package name, since this is
    the directory where **conda-smithy** will be looking for the recipe file
    ``meta.yaml`` (for example, recipes for **sixtools** package must be placed
    in ``~/src/nsls-ii-forge/sixtools`` directory).

  - ``~/src/nsls-ii-forge/<package-name>-feedstock`` -- a directory that
    contains the feedstock repository, which is uploaded to the corresponding
    repository in the **nsls-ii-forge** GitHub organization.  The directory is
    created by **conda-smithy** as a part of feedstock initialization.

==================================
Set up the development environment
==================================

Install "conda-smithy"
======================

Create a new conda environment (let's name it ``smithy``):

.. code-block:: bash

    $ conda create -n smithy -c conda-forge conda-smithy "python>=3"
    $ conda activate smithy


Install Keybase
===============

Use the `installation instructions <https://keybase.io/download>`_ to download
and install **Keybase**. Start **Keybase** and log into the system (DAMA
group). Check if tokens are downloaded to the directory on your local hard
drive (``/keybase/team/dama/`` on Linux and MacOS systems).

Configure "conda-smithy"
========================

Create the directory ``~/.conda-smithy`` (in your home directory). 

.. code-block:: bash

    $ mkdir ~/.conda-smithy

Copy tokens named ``anaconda.token``, ``azure.token`` and ``github.token`` to
``~/.conda-smithy``:

.. code-block:: bash

    $ cp /keybase/team/dama/anaconda.token ~/.conda-smithy/
    $ cp /keybase/team/dama/azure.token ~/.conda-smithy/
    $ cp /keybase/team/dama/github.token ~/.conda-smithy/

Create a root directory for packages
====================================

Naming and location of the root working directory is arbitrary. We will place
packages in the directory ``~/src/nsls-ii-forge/``:

.. code-block:: bash

    $ mkdir -p ~/src/nsls-ii-forge

Clone the "event-model-feedstock" repository from GitHub
========================================================

The **event-model-feedstock** needs to be cloned only once. In the process of
creating packages we will be using the ``conda_build_config.yaml`` file located
in ``~/src/nsls-ii-forge/event-model-feedstock/recipe/`` directory.  If you
already have it cloned, pull the latest version of the
**event-model-feedstock** since the repository may be updated.

.. code-block::

    $ cd ~/src/nsls-ii-forge
    $ git clone https://github.com/nsls-ii-forge/event-model-feedstock.git


=============================================================
Generate and edit the ``meta.yaml`` recipe configuration file
=============================================================

There are several ways to create the package recipe:

- use the existing ``meta.yaml`` recipe file (e.g., from a **conda-forge**
  feedstock repository);

- generate a recipe from the existing PyPI package using **conda skeleton**;

- manually create a ``meta.yaml`` recipe file.


.. note::

    It is important that each of the following command is run from the correct
    directory. Since it is easy to lose track of directory changes, all the
    paths will be specified relative to the ``$HOME`` directory.


Generate a recipe from a PyPI package
=====================================

If the package ``<package-name>`` is available from PyPI, generate a recipe
from the existing package:

.. code-block:: bash

    $ cd ~/src/nsls-ii-forge
    $ conda skeleton pypi <package-name> --noarch

Check if ``meta.yaml`` was successfully created in the
``~/src/nsls-ii-forge/<package-name>`` directory.

Edit the ``meta.yaml`` file:

- Remove all entries from the ``requirements: host:`` section except
  ``python`` and ``pip``.

- Add ``requires:`` and ``commands:`` to the ``test:`` section
  (if applicable).

  .. code-block::

    requires:
      - pytest
    commands:
      - pytest --pyargs sixtools.tests

- Remove the following lines from the ``about:`` section:

  .. code-block::

    doc_url: ""
    dev_url: ""

- Additional steps:

  - Add ``LICENSE`` to the ``license_file_name`` field of the ``about:``
    section.

  - Update the home URL in ``about:`` section to point to the package's GitHub
    repository or a dedicated web site if it exists.

  - Remove the ``extra:`` block (including the list of maintainers).

An example of the edited ``meta.yaml`` for the **sixtools** package may be
found at the `sixtools-feedstock repository
<https://github.com/nsls-ii-forge/sixtools-feedstock/blob/master/recipe/meta.yaml>`_.

Manually create a recipe or use the existing recipe
===================================================

This is an alternative method of preparing the recipe if the package is not
available at PyPI. Create a temporary directory:

.. code-block:: bash

    $ cd ~/src/nsls-ii-forge
    $ mkdir <package-name>
    $ cd <package-name>

The recipe may be created based on ``meta.yaml`` file from the respective
**conda-forge** feedstock (and sometimes from the original package repository).
If such a file is not available or unusable, find an appropriate sample
``meta.yaml`` (from a similar package that was successfully built) and modify
it.  Copy ``meta.yaml`` file to the temporary directory you just created:

.. code-block:: bash

    $ cp <path-to-meta-yaml-file>/meta.yml .

or download ``meta.yaml`` from a known URL:

.. code-block:: bash

    $ wget https://<url-of-meta-yaml-file>/meta.yaml

Open and edit the ``meta.yaml`` file.

====================
Prepare recipe files
====================

Collect additional files
========================

Copy the ``conda_build_config.yaml`` file into your recipe directory:

.. code-block:: bash

    $ cd ~/src/nsls-ii-forge
    $ cp event-model-feedstock/recipe/conda_build_config.yaml <package-name>/

Open and inspect ``conda_build_config.yaml``:

.. code-block:: bash

    $ cd ~/src/nsls-ii-forge/<package-name>
    $ emacs conda_build_config.yaml &

This is the contents of a typical ``conda_build_config.yaml`` file:

.. code-block::

    channel_sources:
    - nsls2forge,defaults
    channel_targets:
    - nsls2forge main
    python:
    - '3.6'

If you are building a ``noarch`` package, then close the file without change.
For an architecture-dependent package, a set of Python versions are specified
in the ``meta.yaml`` file and the following lines should be removed:

.. code-block::

    python:
    - '3.6'

Copy the license file from the original package repository into the recipe
directory.  The license file is typically named ``LICENSE`` (without an
extension), but in some projects the name may differ (e.g. ``COPYRIGHT``). The
spelling of the license file name should match the name specified in the
``license_file_name`` field of the ``about:`` section of the ``meta.yaml`` file
(see the instructions above). For example, the BSD license used for the Bluesky
project may be copied to recipes as follows:

.. code-block:: bash
    
    $ cd ~/src/nsls-ii-forge/<package-name>
    $ wget https://raw.githubusercontent.com/bluesky/bluesky/master/LICENSE


Now the content of the recipe directory ``~/src/nsns-ii-forge/<package-name>``
should look similar to this:

.. code-block:: bash

    drwxr-xr-x 2 user user 4096 Sep 13 12:45 .
    drwxr-xr-x 3 user user 4096 Sep 13 12:10 ..
    -rw-r--r-- 1 user user   96 Sep 13 12:44 conda_build_config.yaml
    -rw-r--r-- 1 user user 1584 Sep 13 12:45 LICENSE
    -rw-r--r-- 1 user user 1064 Sep 13 12:10 meta.yaml

======================================
Generate an empty feedstock repository
======================================

Initialize a feedstock
======================

Initialize a feedstock using **conda-smithy**:

.. code-block:: bash

    $ cd ~/src/nsls-ii-forge
    $ conda-smithy init <package-name>

A new directory ``~/src/nsls-ii-forge/<package-name>-feedstock`` is created.

Replace ``conda-forge.yml`` in the feedstock directory with ``conda-forge.yml``
from the ``event-model-feedstock`` package:

.. code-block:: bash

    $ cd ~/src/nsls-ii-forge
    $ cp event-model-feedstock/conda-forge.yml <package-name>-feedstock/

Define Azure variables
======================

.. code-block:: bash

    $ export AZURE_ORG_OR_USER=nsls2forge
    $ export AZURE_PROJECT_NAME=nsls2forge

For convenience, the above lines may be added to the ``~/.bashrc`` file so that
the environment variables are always available.

Create a GitHub repository and push files
=========================================

.. code-block:: bash

    $ cd ~/src/nsls-ii-forge/<package-name>-feedstock
    $ git add .
    $ git commit -m "Use nsls2forge's conda-forge.yml"
    $ conda smithy register-github --organization nsls-ii-forge ./
    $ git push -u upstream master

Enable CI on Azure Pipelines
============================

.. code-block:: bash

    $ conda smithy register-ci --organization nsls-ii-forge --without-circle \
    --without-appveyor --without-travis --without-drone --feedstock_directory ./

Verify that CI was enabled on Azure Pipelines. Check for the following line in
the output:

.. code-block::

    * nsls-ii-forge/<package-name>-feedstock has been enabled on azure pipelines

==========================================
Rerender and push the feedstock repository
==========================================

Rerender the feedstock
======================

Create a new branch ``rerender``:

.. code-block:: bash

    $ git checkout -b rerender

Rerender the feedstock:

.. code-block:: bash

    $ conda smithy rerender --feedstock_directory ./

.. note::

    From time to time ``conda-smithy`` may inform a user that its version is
    out-of-date, e.g.:

    .. code-block:: python

        RuntimeError: conda-smithy version in root env (3.4.6) is out-of-date (3.4.7). Exiting.

    Update ``conda-smithy`` to use the latest features:

    .. code-block:: bash

        $ conda install conda-smithy -c conda-forge

Commit the changes (the following command should be copied from the terminal
output produced by the previous command):

.. code-block:: bash

    $ git commit -m "MNT: Re-rendered with conda-build 3.18.9, conda-smithy 3.4.6, and conda-forge-pinning 2019.09.08"

Push changes to `upstream`:

.. code-block:: bash

    $ git push -u upstream rerender

===================================================================
Associate Anaconda token from variable groups with the new pipeline
===================================================================

- Log into `dev.azure.com <https://dev.azure.com>`_. 
- Go to the `nsls2forge <https://dev.azure.com/nsls2forge/nsls2forge/_build>`_
  pipelines.  - Select the pipeline named ``<package-name>-feedstock``.
- Click ``Edit``.
- Click the button with three vertical dots in the right top corner.
- Select ``Triggers`` in the drop-down menu.
- Open ``Variables`` tab.
- Select ``Variable groups``.
- Click the ``Link variable group`` button.
- Select ``Anaconda token`` and link it to the pipeline.
- Save changes (do not queue).

===============================
Create a pull request on GitHub
===============================

.. note::

    The Anaconda token must be associated with the new Azure Pipeline before a
    pull request is merged. It is a good practice to associate the token before
    a pull request is created.

Open the GitHub page
``https://github.com/nsls-ii-forge/<package-name>-feedstock`` and create a pull
request. In the pull request comments include a brief note and **the link to
the original repository** of the package (PyPI, conda-forge or GitHub).

Closely examine the build results to ensure that the packages were built for
all target platforms and Python versions and all tests passed successfully.
Correct issues if necessary.  Each time a change is made to configuration
files, the feedstock must be rerendered and changes must be committed and
pushed. Merge the pull request once all issues are fixed and the build statuses
are :raw-html:`<font color="green">green</font>`.

Once the PR build is finished, the new package or its new version will be
uploaded to the `Anaconda Cloud <https://anaconda.org>`_.


========================
Known or possible issues
========================

Tests fail for Linux due to a missing OpenGL
============================================

The solution is to place the file `yum_requirements.txt
<https://raw.githubusercontent.com/nsls-ii-forge/collection-feedstock/master/recipe/yum_requirements.txt>`_
into the recipes directory
``~/src/nsls-ii-forge/<package-name>-feedstock/recipes``, then rerender, commit
and push changes.
