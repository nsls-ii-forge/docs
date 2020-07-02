.. role:: raw-html(raw)
   :format: html

#########
Utilities
#########

There are additional utilities for handling feedstocks at `nsls-ii-forge <https://github.com/nsls-ii-forge>`_.

Here is a list of current functioning utilties:

* check-results
  
  * Check the various parameters of a generated conda package

* all-feedstocks
  
  * Clone/List all available feedstock repositories




============
Installation
============

At the command line::

    $ pip install nsls2forge-utils

=====
Usage
=====

check-results
=============

At the command line::

	$ check-results [-h] [-t {channels,version}] [-f FORBIDDEN_CHANNEL]
              		[-c CMD] [-i] [-p PACKAGE] [-e EXPECTED_VERSION]

optional arguments:

	-h, --help

		show this help message and exit

	-t {channels, version}, --check-type {channels, version}

	    a type of check to perform. One of channels, version

	-f FORBIDDEN_CHANNEL, --forbidden-channel FORBIDDEN_CHANNEL

	    a channel to warn about if it is found in the package list in a conda environment

	-c CMD, --cmd CMD

		a command to check a list of packages in a conda environment

	-i, --ignore-exception

	    a flag to print the list of packages from the channels which are forbidden and proceed without exiting if set to True

	-p PACKAGE, --package PACKAGE

	    a package to check the version for

	-e EXPECTED_VERSION, --expected-version EXPECTED_VERSION

	    minimum expected version of the package


all-feedstocks
==============



