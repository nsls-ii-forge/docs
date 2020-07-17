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
  
  * Clone/List all available feedstock repositories and get info from them

* dashboard

  * Create a dashboard of package feedstocks for nsls-ii-forge

* meta-utils

  * Extract and operate on information from meta.yaml feedstock files

* graph-utils

  * Create a dependency graph of feedstock packages or query information from an existing one

.. note::

    A dependency graph is a directed acyclic graph that contains a node for each software package.
    The parents of a node (higher up in the graph) are packages that the node's package requires
    to build, host, or run. See example usage below.


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

``.netrc`` Setup
================

This may already be setup if you automatically authenticate on GitHub using git.

If not, create a file and open it for editing:

.. code-block:: bash

    $ vi ~/.netrc

Place the following in that file with your GitHub username and access token.

.. code-block:: bash

    machine github.com
    login [USERNAME]
    password [ACCESS_TOKEN]

To save the file and quit hit ESC, type :bash:`wq` and hit ENTER.

To make this file owner-readable only, use:

.. code-block:: bash

    $ chmod 600 ~/.netrc


conda-smithy Setup
==================

Cloning repositories requires authentication through conda-smithy.
To install conda-smithy, run:

.. code-block:: bash

    $ conda install -c conda-forge conda-smithy

Now place the token in the correct file:

.. code-block:: bash

    $ vi ~/.conda-smithy/github.token

To save the file and quit hit ESC, type :bash:`wq` and hit ENTER.

To make this file owner-readable only, use:

.. code-block:: bash

    $ chmod -Rv go-rwx ~/.conda-smithy

.. note::

    Authentication using :bash:`~/.conda-smithy/github.token` is only required
    when cloning feedstock repositories.

conda-forge-tick Setup
======================

Some utilities require functionality from the `conda-forge-tick` package.

The repository can be found `here <https://github.com/regro/cf-scripts>`_.

To install this package, use the following commands:

.. code-block:: bash

    $ git clone --depth 1 https://github.com/regro/cf-scripts.git
    $ cd ./cf-scripts
    $ python setup.py install

.. note::

    Cloning this repository is the only method of installing it since
    there is no tagged version available at the moment.


==============
Usage Examples
==============

check-results
=============

First, set up a conda environment.

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

You can include all archived feedstocks with the :bash:`-a` or :bash:`--archived` flag.
The default behavior is to ignore archived feedstocks.

.. note::

    You can manually specify the username and personal access token for listing all feedstocks
    with :bash:`--username` and :bash:`--token` instead of :bash:`~/.netrc`.

To clone all feedstocks belonging to a GitHub organization, use:

.. code-block:: bash

    $ all-feedstocks -o nsls-ii-forge clone

This will clone all feedstocks to the :bash:`feedstocks/` directory in your current working directory.

To get information about Git and version information from currently cloned feedstock repos, use:

.. code-block:: bash

    $ all-feedstocks info


For more information on possible usage:

.. code-block:: bash

    $ all-feedstocks -h
    $ all-feedstocks list -h
    $ all-feedstocks clone -h
    $ all-feedstocks info -h

dashboard
=========

To create a dashboard of package feedstocks for nsls-ii-forge, use:

.. code-block:: bash

    $ dashboard

.. note::

    This uses :bash:`~/.netrc` for GitHub authentication.

You can optionally provide a list of feedstock package names from a
text file:

.. code-block:: bash

    $ dashboard -n names.txt

You can also specify the output file name (default is README.md):

.. code-block:: bash

    $ dashboard -w output.md

For more information on possible usage:

.. code-block:: bash

    $ dashbord -h

meta-utils
==========

To get any attribute of a feedstock packages :bash:`meta.yaml` file, use :bash:`-g` or :bash:`--get`:

Examples:

Getting the source url for event-model

.. code-block:: bash

    $ meta-utils -o nsls-ii-forge -p event-model -g source url
    source url: https://pypi.io/packages/source/e/event-model/event-model-1.15.2.tar.gz

Getting the package info for event-model

.. code-block:: bash

    $ meta-utils -o nsls-ii-forge -p event-model -g package
    package: {'name': 'event-model', 'version': '1.15.2'}

Getting the requirements to run event-model:

.. code-block:: bash

    $ meta-utils -o nsls-ii-forge -p event-model -g requirements run
    requirements run: ['python >=3.6', 'jsonschema', 'numpy']

To download a package from its source url, use:

.. code-block:: bash

    $ meta-utils -o nsls-ii-forge -p event-model -d
    Successfully downloaded https://pypi.io/packages/source/e/event-model/event-model-1.15.2.tar.gz
    sha256: 31b6103801abcc3ebe099757bca7c9da9b4d535330acf0ba10d81c0753eb0e51

For more information on possible usage:

.. code-block:: bash

    $ meta-utils -h

graph-utils
===========

To create/update a dependency graph with new package feedstocks, use:

.. code-block:: bash

    graph-utils make -o nsls-ii-forge

This will store the result in a JSON file :bash:`graph.json`.

To load the graph in your python script, use:

.. code-block:: python

    import json
    import networkx as nx
    j = json.load('graph.json')
    graph = nx.node_link_graph(j)

To query information from the graph, use:

.. code-block:: bash

    $ graph-utils info -p event-model -q depends_on
    The following packages require event-model to be installed:
    analysis
    bluesky
    bluesky-darkframes
    databroker
    databroker-pack
    shed
    suitcase-csv
    suitcase-json-metadata
    suitcase-jsonl
    suitcase-mongo
    suitcase-msgpack
    suitcase-specfile
    suitcase-tiff
    xicam
    xicam.xpcs
    Total: 15
    $ graph-utils info -p event-model -q depends_of
    event-model requires the following packages to be installed:
    jsonschema
    numpy
    pip
    pytest
    python
    Total: 5

:bash:`depends_on` and :bash:`depends_of` are currently the only two types of queries.

To update nodes in the graph with new versions from their sources, use:

.. code-block:: bash

    $ graph-utils update

This will pull new version numbers from various sources (PyPI, Github, etc.) and update each node with
a tag "new_version".

In the near future, there will be a bot that will handle packages with "new_version" greater than "version"
and submit pull requests automatically to update the appropriate feedstock repostiories.

For more information on possible usage:

.. code-block:: bash

    $ graph-utils -h
    $ graph-utils make -h
    $ graph-utils info -h
    $ graph-utils update -h
