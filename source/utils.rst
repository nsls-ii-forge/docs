.. role:: raw-html(raw)
   :format: html

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

==============
Usage Examples
==============

check-results
=============

First set up a conda environment.

.. code-block:: bash

	$ conda create -n myenv
    $ conda activate myenv

Install packages from anaconda.

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

For more information on possible usage use:

.. code-block:: bash

	$ check-results -h

all-feedstocks
==============




