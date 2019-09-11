# Prepare

```bash
$ conda create -n smithy -c conda-forge conda-smithy

```

## Copy tokens

```bash
$ ls /keybase/team/dama/
anaconda.token
azure.token
github.token
...
```

Create directory ~/.conda-smithy and place these three files in that Place these files in .conda-smithy.
```bash
$ mkdir .conda-smithy
$ cp /keybase/team/dama/anaconda.token .conda-smithy/
$ cp /keybase/team/dama/azure.token .conda-smithy/
$ cp /keybase/team/dama/github.token .conda-smithy/
```

## Generate a (noarch) recipe

$ conda activate smithy
(smithy) $ mkdir recipes
(smithy) $ cd recipes/
(smithy) $ conda skeleton pypi sixtools --noarch

## Edit meta.yml
$ vi recipes/sixtools/meta.yml

Remove all entries from host section except python and pip.
Add requires and commands to the test section. 
```
  requires:
    - pytest
  commands:
    - pytest --pyargs sixtools.tests
```
Remove these lines from about:.
```
  doc_url: ""
  dev_url: ""
```

Copy license file from the project repository to the sixtools directory.
Add license file name to about section.
Update URL.
Remove maintainers.
Remove extra block.

Before:
```yaml
requirements:
  host:
    - databroker
    - ipywidgets
    - matplotlib
    - numpy
    - pandas
    - pims
    - pip
    - python
    - rixs
    - scipy
  run:
    - databroker
    - ipywidgets
    - matplotlib
    - numpy
    - pandas
    - pims
    - python
    - rixs
    - scipy

test:
  imports:
    - sixtools
    - sixtools.test

about:
  home: "https://github.com/awalter-bnl/sixtools"
  license: "BSD (3-clause)"
  license_family: "BSD"
  license_file: ""
  summary: "Software for performing resonant inelastic xray scattering analysis at NSLS-II"
  doc_url: ""
  dev_url: ""

extra:
  recipe-maintainers:
    - your-github-id-here

```
After:
```yaml
requirements:
  host:
    - python
    - pip
  run:
    - python
    - databroker
    - ipywidgets
    - matplotlib
    - numpy
    - pandas
    - pims
    - rixs
    - scipy

test:
  imports:
    - sixtools
  requires:
    - pytest
  commands:
    - pytest --pyargs sixtools.tests

about:
  home: "https://github.com/NSLS-II-SIX/sixtools"
  license: "BSD (3-clause)"
  license_family: "BSD"
  license_file: "LICENSE"
  summary: "Software for performing resonant inelastic xray scattering analysis at NSLS-II"
```

```bash
git clone https://github.com/nsls-ii-forge/event-model-feedstock.git
cp event-model-feedstock/recipe/conda_build_config.yaml ~/project/sixtools-tmp/sixtools/
```

```bash
(smithy) recipes/sixtools$ ls -l
total 12
-rw-r--r-- 1 jlynch jlynch   96 Sep 11 14:51 conda_build_config.yaml
-rw-r--r-- 1 jlynch jlynch 1584 Sep 11 14:36 LICENSE
-rw-r--r-- 1 jlynch jlynch  964 Sep 11 14:49 meta.yaml
```

## Initialize feedstock

```bash
(smithy) thorisdottir:sixtools-tmp joshualynch$ conda-smithy init sixtools
No numpy version specified in conda_build_config.yaml.  Falling back to default numpy value of 1.11
Initialized empty Git repository in /Users/joshualynch/project/sixtools-tmp/sixtools-feedstock/.git/
[master (root-commit) c214512] Initial feedstock commit with conda-smithy 3.4.5.
 4 files changed, 80 insertions(+)
 create mode 100644 conda-forge.yml
 create mode 100644 recipe/LICENSE
 create mode 100644 recipe/conda_build_config.yaml
 create mode 100644 recipe/meta.yaml

Repository created, please edit conda-forge.yml to configure the upload channels
and afterwards call 'conda smithy register-github'
```

Replace conda-forge.yml with event-model conda-forge.yml.

```bash
$ cp event-model-feedstock/conda-forge.yml recipes/sixtools
```

## Define azure environment variables

```bash
# nsls-ii-forge vars:
export AZURE_ORG_OR_USER=nsls2forge
export AZURE_PROJECT_NAME=nsls2forge
```
## Create GitHub repository

```bash
conda smithy register-github --organization nsls-ii-forge ./
```

## Push files

```bash
$ git push -u upstream master
```

From recipes/sixtools-feedstock directory:

```bash
$ conda smithy register-ci --organization nsls-ii-forge --without-circle --without-appveyor --without-travis --feedstock_directory ./
```

## Rerender feedstock

```bash
$ conda smithy rerender --feedstock_directory .
$ git status
On branch master
Your branch is up to date with 'upstream/master'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   .azure-pipelines/azure-pipelines-linux.yml
	new file:   .azure-pipelines/build_steps.sh
	new file:   .azure-pipelines/run_docker_build.sh
	new file:   .ci_support/README
	new file:   .ci_support/linux_.yaml
	new file:   .circleci/config.yml
	new file:   .gitattributes
	new file:   .gitignore
	new file:   LICENSE.txt
	new file:   README.md
	new file:   azure-pipelines.yml
	new file:   build-locally.py
```

## Commit to branch 'rerender'

```bash
(smithy) thorisdottir:sixtools-feedstock joshualynch$ git checkout -b rerender
A	.azure-pipelines/azure-pipelines-linux.yml
A	.azure-pipelines/build_steps.sh
A	.azure-pipelines/run_docker_build.sh
A	.ci_support/README
A	.ci_support/linux_.yaml
A	.circleci/config.yml
A	.gitattributes
A	.gitignore
A	LICENSE.txt
A	README.md
A	azure-pipelines.yml
A	build-locally.py
Switched to a new branch 'rerender'
(smithy) $ git commit -m "MNT: Re-rendered with conda-build 3.18.9, conda-smithy 3.4.5, and conda-forge-pinning 2019.09.08"
[rerender 21f8fc3] MNT: Re-rendered with conda-build 3.18.9, conda-smithy 3.4.5, and conda-forge-pinning 2019.09.08
 12 files changed, 369 insertions(+)
 create mode 100755 .azure-pipelines/azure-pipelines-linux.yml
 create mode 100755 .azure-pipelines/build_steps.sh
 create mode 100755 .azure-pipelines/run_docker_build.sh
 create mode 100644 .ci_support/README
 create mode 100644 .ci_support/linux_.yaml
 create mode 100644 .circleci/config.yml
 create mode 100644 .gitattributes
 create mode 100644 .gitignore
 create mode 100644 LICENSE.txt
 create mode 100644 README.md
 create mode 100644 azure-pipelines.yml
 create mode 100755 build-locally.py
(smithy) $ git status
On branch rerender
nothing to commit, working tree clean
(smithy) $ git push -u upstream rerender
Enumerating objects: 18, done.
Counting objects: 100% (18/18), done.
Delta compression using up to 12 threads
Compressing objects: 100% (15/15), done.
Writing objects: 100% (17/17), 6.93 KiB | 3.47 MiB/s, done.
Total 17 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), done.
remote:
remote: Create a pull request for 'rerender' on GitHub by visiting:
remote:      https://github.com/nsls-ii-forge/sixtools-feedstock/pull/new/rerender
remote:
To github.com:nsls-ii-forge/sixtools-feedstock.git
 * [new branch]      rerender -> rerender
Branch 'rerender' set up to track remote branch 'rerender' from 'upstream'.
```
