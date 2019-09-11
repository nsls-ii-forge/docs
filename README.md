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

git clone https://github.com/nsls-ii-forge/event-model-feedstock.git
cp event-model-feedstock/recipe/conda_build_config.yaml ~/project/sixtools-tmp/sixtools/

(smithy) jlynch@thorisdottir:~/host/project/sixtools-tmp/sixtools$ ls -l
total 12
-rw-r--r-- 1 jlynch jlynch   96 Sep 11 14:51 conda_build_config.yaml
-rw-r--r-- 1 jlynch jlynch 1584 Sep 11 14:36 LICENSE
-rw-r--r-- 1 jlynch jlynch  964 Sep 11 14:49 meta.yaml

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
$ git push -u upstream master
