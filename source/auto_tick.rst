.. role:: raw-html(raw)
   :format: html
.. role:: bash(code)
   :language: bash

#############
Auto-tick-bot
#############

This bot uses nsls2forge-utils to update versions of packages
automatically at the nsls-ii-forge GitHub organization.


It currently runs on `Azure Pipelines <https://dev.azure.com/nsls2forge/nsls2forge/_build?definitionId=236>`_ on a daily schedule.


The setup and execution scripts can be found at the `auto-tick-bot <https://github.com/nsls-ii-forge/auto-tick-bot>`_
repository on GitHub.

===========
The Process
===========

The auto-tick-bot peforms a couple of different steps.

1. Retrieve the cached version of the dependency graph from `auto-tick-graph <https://github.com/nsls-ii-forge/auto-tick-graph>`_

2. Update the graph with any new or updated feedstocks

3. Get new versions of packages from their sources (PyPI, GitHub, etc.) and update in graph

4. Perform a dry-run of the version migration and queue packages to be migrated

5. Perform a full run of the version migration and submit PRs for up to 10 packages per run

6. Output status of PRs and migrations

7. Push the changes to the graph back to `auto-tick-graph <https://github.com/nsls-ii-forge/auto-tick-graph>`_

8. Clean the current directory

If the bot fails during a run on Azure Pipelines, please remove the current schedule or disable the pipeline until the issue is fixed.


.. note::

	If this process fails at step 5 or 6, there may be multiple pull requests submitted for a single
	version update for a feedstock. This is because the :bash:`pr_json` object that is created and assigned to the
	package does not get pushed to the cached `auto-tick-graph <https://github.com/nsls-ii-forge/auto-tick-graph>`_.
	Closing or Merging these PRs before the bot runs again is recommended.


Some packages may run into other types of errors. These will be displayed in the log on Azure Pipelines as well as in
the `version status JSON file <https://github.com/nsls-ii-forge/auto-tick-graph/blob/master/status/version_status.json>`_.

Do not remove files manually from `auto-tick-graph <https://github.com/nsls-ii-forge/auto-tick-graph>`_ unless you know what you are doing.
