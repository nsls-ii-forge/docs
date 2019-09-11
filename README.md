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
