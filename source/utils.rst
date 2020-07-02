.. role:: raw-html(raw)
   :format: html
.. role:: bash(code)
   :language: bash

#########
Utilities
#########

Utilities for handling feedstocks at `nsls-ii-forge <https://github.com/nsls-ii-forge>`_.

Here is a list of current functioning utilties:

* check-results
  
  * Check the various parameters of a generated conda package

* all-feedstocks
  
  * Clone/List all available feedstock repositories

============
Installation
============

At the command line

.. code-block:: bash

    $ pip install nsls2forge-utils

=====================
GitHub Authentication
=====================

Authentication is done through :bash:`~/.netrc` and :bash:`~/.conda-smithy/github.token`.

GitHub Access Token
===================

First, create a `personal access token <https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token>`_ for GitHub.

netrc Setup
===========

This may already be setup if you automatically authenticate GitHub using git.

If not, create a file and open it for editing:

.. code-block:: bash

    $ vi ~/.netrc

Place the following in that file with your GitHub username and access token.

.. code-block:: bash

    machine github.com
    login [USERNAME]
    password [ACCESS_TOKEN]

To save the file and quit hit ESC, type :bash:`wq` and hit ENTER.

conda-smithy Setup
==================

Cloning repositories requires authentication through conda-smithy.
To install conda-smithy run:

.. code-block:: bash

    $ conda install -c conda-forge conda-smithy

Now place the token in the correct file:

.. code-block:: bash

    $ vi ~/.conda-smithy/github.token

To save the file and quit hit ESC, type :bash:`wq` and hit ENTER.

.. note::

    Authentication using :bash:`~/.conda-smithy/github.token` is only required
    when cloning feedstock repositories.

==============
Usage Examples
==============

check-results
=============

First set up a conda environment.

.. code-block:: bash

    $ conda create -n myenv
    $ conda activate myenv
    $ conda install -c defaults pip

Install nsls2forge packages from PyPI.

.. code-block:: bash

    $ pip install event-model

You can check against a specific version of packages you are interested in with:

.. code-block:: bash

    $ check-results -t version -p event-model -e 1.0

This will provide output like this:

.. code-block:: bash

    The found version (1.15.2) of "event_model" is more or equal the expected version (1.0)

You can also check for packages from a specific channel with:

.. code-block:: bash

    $ check-results -t channels -f conda-forge

This will list all installed packages from the conda-forge channel.

.. note::

    The previous command will fail if packages are found
    from the specified channel.

    To ignore this behavior use :bash:`-i`.

For more information on possible usage:

.. code-block:: bash

    $ check-results -h

all-feedstocks
==============

To list all feedstock repositories belonging to a GitHub organization use:

.. code-block:: bash

    $ all-feedstocks -o nsls-ii-forge list

This will output a sorted list of repo names ending with :bash:`-feedstock`.

You can write these names to a file with the :bash:`-w` or :bash:`--write` flag.

.. note::

    You can manually specify the username and personal access token for listing all feedstocks
    with :bash:`--username` and :bash:`--token` instead of :bash:`~/.netrc`.

To clone all feedstocks belonging to a GitHub organization use:

.. code-block:: bash

    $ all-feedstocks -o nsls-ii-forge clone

This will clone all feedstocks to :bash:`/feedstocks` in your current working directory.

For more information on possible usage:

.. code-block:: bash

    $ all-feedstocks -h
    $ all-feedstocks list -h
    $ all-feedstocks clone -h
