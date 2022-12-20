# Introduction

Forked from https://github.com/stgraber/lxd-github-actions

This repository contains some scripts to create LXD instances suitable
to be used as Github Actions runners as well as tools to spawn a set of
such instances.

This will use LXD with a set of ephemeral instances which will then be
added as Github Runners. Each of those will process exactly one job and
then self-destroy. This is to avoid running jobs in unclean
environments, preventing one job from impacting or attacking the next.


# Creating a suitable base instance

For example, for an Ubuntu 20.04 container, you'd use:
```
lxc launch images:ubuntu/20.04 gh-runner
```

### Do Once : 

Prepare the LXD container with all its depedencies 
```
./prepare-instance gh-runner
```

Setup a PAT (Personal access token) with access to the repos and orgs you want serviced. 
https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token
- Enable the following scopes:
	- repo
	-  workflow
	-  admin:org
	-  admin:repo_hook

# Starting some runners

### Create cronjob : 

Create a cronjob that executes the following job :

```
./respawn gh-runner RUNNER_COUNT https://github.com/ORG/REPO PAT label1,label2
```

- gh-runner (or the name of your base container)
- RUNNER_COUNT : (number of gh-runners that will run in parallel)
- repository address
- PAT (starting with ghg_...)
- LABELS (optinal)

``` bash
crontab -e

>>>
* * * * * /path/to/lxd-runner/respawn gh-runner RUNNER_COUNT https://github.com/ORG/REPO PAT 
```
