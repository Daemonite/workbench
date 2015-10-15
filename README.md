# Daemonite Docker Development Workbench

_Note this is a documentation project for setting up a dockerised development environment with Vagrant and Virtualbox.  Don't just clone and run because you won't get far ;)_

## Installation

> Enough babble.. just get on with it!
> _Anonymous_

1. install Git client (>1.6.5)
2. install Virtualbox (>5.x)
3. install Vagrant (>1.7.3)
4. install hostmanager plugin: `$ vagrant plugin install hostmanager`
5. create a local Projects directory (can be called anything): `$ mkdir ~/Projects`
6. copy Workbench VM Vagrantfile into Projects directory: https://github.com/Daemonite/workbench/blob/master/Vagrantfile

Test installation and make sure everything is running by bringing up the Workbench DockerUI view:
```
cd ~/Projects
vagrant up
open http://workbench:81
```

Test drive a project:
```
cd ~/Projects
git clone --recursive git@github.com:modius/chelsea-docker.git
cd chelsea-docker
vagrant up --no-parallel
open http://workbench:8009/
```

## Overview

The Workbench assumes a simple hierarchy of Docker projects.  Each app or Docker image is isolated in its own Git repo.  Developers clone environment repos into their Projects folder.

```
~/Projects
├── Vagrantfile <-- Boot2Docker VM
├── aoc-env-corporate <-- git repo
│   ├── Dockerfile
│   ├── README.md
│   ├── Vagrantfile
│   ├── code
│   ├── config
│   └── logs
├── aoc-env-mediacentre <-- git repo
│   ├── Dockerfile
│   ├── README.md
│   ├── Vagrantfile
│   ├── code
│   ├── config
│   └── logs
└── aoc-env-rio2016 <-- git repo
    ├── Dockerfile
    ├── README.md
    ├── Vagrantfile
    ├── code
    ├── config
    └── logs
```

At the root of the Projects folder is a Vagrantfile that defines the Workbench VM; Boot2Docker provisioned with useful tools.  Each environment project has its own Vagrantfile that looks for the shared Workbench VM (./Projects/Vagrantfile) where it builds and deploys containers.

The makeup of a specific project environment repo is detailed elsewhere.

