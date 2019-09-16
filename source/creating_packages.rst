#################
Creating Packages
#################

Feedstocks for Azure pipelines may be generated based on
existing PyPI packages or conda-forge feedstocks, or directly from GitHub
package repositories.

===================
Directory structure
===================

The following directory structure for the packaging project
is recommended:

``~/src/nsls-ii-forge/`` - root directory in which packages will be
placed. This is only a recommended name. In practice, root
directory may have arbitrary name and placed in any convenient spot.

``~/src/nsls-ii-forge/event-model-feedstock`` -
the directory with cloned **event-model-feedstock** repository.

For each package we will create a temporary directory that may be deleted
after a feedstock is initialized and the feedstock directory that will contain
the feedstock repository:

``~/src/nsls-ii-forge/<package_name>`` -
a temporary directory with the package's recipe. The directory is
created by **conda skeleton** or manually. The directory name has to
match the package name, since this is the directory where
**conda-smithy** will be looking for recipe
(for example recipes for **sixtools** package
must be placed in ``~/src/nsls-ii-forge/sixtools`` directory).

``~/src/nsls-ii-forge/(package-name)-feedstock`` -
the directory that contains the feedstock repository, which is
uploaded to **nsls-ii-forge**. The directory is created by conda-smithy
as part of feedstock initalization.

==================================
Set up the Development Environment
==================================

Install "conda-smithy"
======================

Create a new conda environment (let's name it ``smithy``):

.. code-block:: bash

    $ conda create -n smithy -c conda-forge conda-smithy "python>=3"
    $ conda activate smithy


Install Keybase
===============

Use `installation instructions <https://keybase.io/download>`_ 
to download and install **Keybase**. Start **Keybase** and log into
the system (DAMA group). Check if tokens are downloaded to
the directory on your local hard drive (``/keybase/team/dama/`` 
on Linux and MacOS systems).

Configure "conda-smithy"
========================

Create the directory ``~/.conda-smithy`` (in your home directory). 

.. code-block:: bash

    $ cd ~
    $ mkdir .conda-smithy

Copy tokens to ``anaconda.token``, ``azure.token`` and ``github.token`` 
to ``~/.conda-smithy``:

.. code-block:: bash

    $ cp /keybase/team/dama/anaconda.token .conda-smithy/
    $ cp /keybase/team/dama/azure.token .conda-smithy/
    $ cp /keybase/team/dama/github.token .conda-smithy/

Create Root Directory for Packages
==================================

Naming and location of the root working directory is arbitrary. We will
place packages in the directory ``~/src/nsls-ii-forge/``:

.. code-block:: bash

    $ cd ~
    $ mkdir src
    $ mkdir src/nsls-ii-forge

Clone *event-model-feedstock* repository from GitHub
====================================================

The **event-model-feedstock** needs to be cloned only once. In the process of
creating packages we will be using file ``conda_build_config.yaml`` located
in ``~/src/nsls-ii-forge/event-model-feedstock/recipe/`` directory.
If you already have it cloned, pull the latest version of the
**event-model-feedstock** since the repository may be updated.

.. code-block::

    $ cd ~/src/nsls-ii-forge
    $ git clone https://github.com/nsls-ii-forge/event-model-feedstock.git


=======================================================
Generate and Edit "meta.yaml" Recipe Configuration File
=======================================================

There are several ways to create the package recipe:

- generate recipe from existing PyPI package;

- manually create ``meta.yaml`` recipe file using **conda skeleton**;

- use existing ``meta.yaml`` recipe file.

.. note::

    It is important that each of the following command
    is run from the correct directory. Since it is easy to lose track of
    directory changes, all the paths will be specified 
    relative to ``HOME`` directory.

Generate recipe from PyPI package
=================================

If the package ``(package-name)`` is available from PyPI,
generate the recipe from the existing package:

.. code-block:: bash

    $ cd ~/src/nsls-ii-forge
    $ conda skeleton pypi (package-name) --noarch

Check if ``meta.yaml`` was successfully created in
``~/src/nsls-ii-forge/(package-name)`` directory.

Edit ``meta.yaml`` file:

Remove all entries from ``requirements: host:`` section except
``python`` and ``pip``.
Add ``requires:`` and ``commands:`` to the ``test:`` section.

.. code-block::

  requires:
    - pytest
  commands:
    - pytest --pyargs sixtools.tests

Remove the following lines from the ``about:`` section:

.. code-block::

  doc_url: ""
  dev_url: ""

Additional steps:

  - Add license file name ``LICENSE`` to ``about:`` section.
  - Update home URL in ``about:`` section to point to
    the package's GitHub repository.
  - Remove the list of maintainers.
  - Remove ``extra:`` block.

The example of edited ``meta.yaml`` for
**sixtools** package may be found at the
`sixtools-feedstock repository <https://github.com/nsls-ii-forge/sixtools-feedstock/blob/master/recipe/meta.yaml>`_.

Manually create recipe or use the existing recipe
=================================================

This is alternative method of preparing the recipe if package is
not available at PyPI. Create temporary directory:

.. code-block:: bash

    $ cd ~/src/nsls-ii-forge
    $ mkdir (package-name)
    $ cd (package-name)

The recipe may be created based on ``meta.yaml`` file from
the original package repository. If such file is not available
or unusable, find an appropriate sample ``meta.yaml`` (from
similar package that was successfully built) and modify it.
Copy ``meta.yaml`` file to the temporary directory you just created:

.. code-block:: bash

    $ cp (path-to-meta-yaml-file)/meta.yml .

or download ``meta.yaml`` from known URL:

.. code-block:: bash

    $ wget https://(url-of-meta-yaml-file)/meta.yaml

Open and edit ``meta.yaml`` file.

.. note::

    TODO: Some notes on editing ``meta.yaml`` file.

====================
Prepare Recipe Files
====================

Collect additional files
========================

Copy ``conda_build_config.yaml`` file into your recipe directory:

.. code-block:: bash

    $ cd ~/src/nsls-ii-forge
    $ cp event-model-feedstock/recipe/conda_build_config.yaml (package-name)/

Open and inspect ``conda_build_config.yaml``:

.. code-block:: bash

    $ cd ~/src/nsls-ii-forge/(package-name)
    $ emacs conda_build_config.yaml &

This is the contents of typical ``conda_build_config.yaml`` file:

.. code-block::

    channel_sources:
    - nsls2forge,defaults
    channel_targets:
    - nsls2forge main
    python:
    - '3.6'

If you are building a noarch package, then close the file without change.
For an arch package, a set of Python versions are specified in
``meta.yaml`` file and the following lines should be removed:

.. code-block::

    python:
    - '3.6'

Copy licence file from the original package repository into the recipe directory.
The file must be named ``LICENSE`` (without extension). For example, BSD license 
used for Bluesky project may be copied to recipes as

.. code-block:: bash
    
    cd ~/src/nsls-ii-forge/(package-name)
    wget https://raw.githubusercontent.com/bluesky/bluesky/master/LICENSE


Now the content of the recipe directory 
``~/src/nsns-ii-forge/(package-name)``
should look similar to this:

.. code-block:: bash

    drwxr-xr-x 2 user user 4096 Sep 13 12:45 .
    drwxr-xr-x 3 user user 4096 Sep 13 12:10 ..
    -rw-r--r-- 1 user user   96 Sep 13 12:44 conda_build_config.yaml
    -rw-r--r-- 1 user user 1584 Sep 13 12:45 LICENSE
    -rw-r--r-- 1 user user 1064 Sep 13 12:10 meta.yaml

===================================
Generate Empty Feedstock Repository
===================================

Initialize feedstock
====================

Initialize feedstock using **conda-smithy**:

.. code-block:: bash

    $ cd ~/src/nsls-ii-forge
    $ conda-smithy init (package-name)

A new directory ``~/src/nsls-ii-forge/(package-name)-feedstock``
is created.

Replace ``conda-forge.yml`` in the feedstock directory with ``conda-forge.yml`` from
``event-model-feedstock`` package:

.. code-block:: bash

    $ cd ~/src/nsls-ii-forge
    $ cp event-model-feedstock/conda-forge.yml (package-name)-feedstock/

Define Azure variables
======================

.. code-block:: bash

    $ export AZURE_ORG_OR_USER=nsls2forge
    $ export AZURE_PROJECT_NAME=nsls2forge


Create GitHub repository and push files
=======================================

.. code-block:: bash

    $ cd ~/src/nsls-ii-forge/(package-name)-feedstock
    $ conda smithy register-github --organization nsls-ii-forge ./
    $ git add .
    $ git commit -m "Initial commit"
    $ git status
    $ git push -u upstream master

Enable CI on Azure pipelines
============================

.. code-block:: bash

    $ conda smithy register-ci --organization nsls-ii-forge --without-circle \
    --without-appveyor --without-travis --without-drone --feedstock_directory ./

Verify that CI was enabled on Azure pipelines. Check for the following line 
in the output:

.. code-block::

    * nsls-ii-forge/inflection-feedstock has been enabled on azure pipelines

======================================
Rerender and Push Feedstock Repository
======================================

Rerender the feedstock
======================

Create new branch ``rerender``:

.. code-block:: bash

    $ git checkout -b rerender

Rerender the feedstock:

.. code-block:: bash

    $ conda smithy rerender --feedstock_directory .

Commit the changes (the following command should be copied from the terminal output
produced by the previous command):

.. code-block:: bash

    $ git commit -m "MNT: Re-rendered with conda-build 3.18.9, conda-smithy 3.4.6, and conda-forge-pinning 2019.09.08"

Push changes to `upstream`:

.. code-block:: bash

    $ git push -u upstream rerender


=============================
Create pull request at GitHub
=============================

Open github page ``https://github.com/nsls-ii-forge/(package-name)-feedstock``
and create pull request. In pull request comments include a brief note and **the link to the original 
repository** of the package (PyPI, conda-forge or GitHub).

Closely examine build results to ensure that the packages were built for all systems
and Python versions and all tests passed successfully. Correct issues if necessary.
Each time a change is made to configuration files, the feedstock must be
rerendered and changes must be committed and pushed. Merge the pull request once
all issues are fixed.

===================================================================
Associate Anaconda Token from Variable Groups With the New Pipeline
===================================================================

- Log into `dev.azure.com <https://dev.azure.com>`_. 
- Select the pipeline named ``(package-name)-feedstock``.
- Click ``Edit``.
- Click the button with three vertical dots in the right top corner.
- Select 'Triggers' in the drop-down menu.
- Open ``Variables`` tab.
- Select 'Variable groups'.
- Click the button 'Link variable groups'.
- Select 'Anaconda token'.

======
Issues
======

Tests fail for Linux system due to missing OpenGL
=================================================

The solution is to place the file 
`yum_requirements.txt <https://raw.githubusercontent.com/nsls-ii-forge/collection-feedstock/master/recipe/yum_requirements.txt>`_
into the recipes directory 
``~/src/nsls-ii-forge/(package-name)-feedstock/recipes``,
then rerender, commit and push changes.
