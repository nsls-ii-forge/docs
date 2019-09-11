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
```
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
```
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
