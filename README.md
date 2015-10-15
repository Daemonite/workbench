# Daemonite Docker Development Workbench

_Note this is a documentation project for setting up a dockerised development environment with Vagrant and Virtualbox.  Don't just clone and run because you won't get far ;)_

## Installation

> Enough babble.. just get on with it!
> _Anonymous_

1. install Git client (>1.6.5)
2. install Virtualbox (>5.x)
3. install [Vagrant (>1.7.4)](https://www.vagrantup.com/downloads.html)
4. install hostmanager plugin: `$ vagrant plugin install hostmanager`
5. create a local Projects directory (can be called anything): `$ mkdir ~/Workbench`
6. copy Workbench VM Vagrantfile into ~/Workbench directory: https://github.com/Daemonite/workbench/blob/master/Vagrantfile

Test installation and make sure everything is running by bringing up the Workbench DockerUI view:
```
cd ~/Workbench
vagrant up
open http://workbench:81
```

_Note, you may be prompted for your admin password by `hostmanager` when it attempts to update your local hosts file._

Test drive a project:
```
cd ~/Workbench
git clone --recursive git@github.com:modius/chelsea-docker.git
cd chelsea-docker
vagrant up --no-parallel
open http://workbench:8009/
```

## Overview

The Workbench assumes a simple hierarchy of Docker projects.  Each app or Docker image is isolated in its own Git repo.  Developers clone environment repos into their Workbench folder.

```
~/Workbench
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

At the root of the Workbench folder is a Vagrantfile that defines the Workbench VM; Boot2Docker provisioned with useful tools.  Each environment project has its own Vagrantfile that looks for the shared Workbench VM (./Workbench/Vagrantfile) where it builds and deploys containers.

The makeup of a specific project environment repo is detailed elsewhere.

