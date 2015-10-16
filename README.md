# Daemonite Docker Development Workbench

_Note this is a documentation project for setting up a dockerised development environment with Vagrant and Virtualbox.  Don't just clone and run because you won't get far ;)_

## Installation

> Enough babble.. i don't want to understand it, just install it!
> _Disgruntled Anonymous_

1. install Git client (>1.6.5)
2. install Virtualbox (>5.x)
3. install [Vagrant (>1.7.4)](https://www.vagrantup.com/downloads.html)
4. install hostmanager plugin: 
   `$ vagrant plugin install hostmanager`
5. create a local projects directory (can be called anything): 
   `$ mkdir ~/Workbench`
6. copy Workbench VM `Vagrantfile` into `~/Workbench` directory, located here in this project: 
   https://github.com/Daemonite/workbench/blob/master/Vagrantfile

Test installation and make sure everything is running by bringing up the Workbench DockerUI view:
```
cd ~/Workbench
vagrant up
open http://workbench:81
```

_Note, you may be prompted for your admin password by `hostmanager` when it attempts to update your local hosts file._

![DockerUI](./images/wb-dockerui.jpg)

Test drive a project:
```
cd ~/Workbench
git clone --recursive git@github.com:modius/chelsea-docker.git
cd chelsea-docker
vagrant up --no-parallel
open http://workbench:8009/
```

![Chelsea: FarCry Core](./images/wb-chelsea.jpg)

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

The makeup of a specific project environment repo is detailed in the specific README of each project skeleton; for example, Lucee Workbench.

## Workbench `Vagrantfile` explained

```
################################################## 
# Daemonite Docker Workbench
# v1.0
##################################################
WORKBENCH_IP = "172.22.22.22"
Vagrant.require_version ">= 1.7.4"

Vagrant.configure("2") do |config|
  # configure hostmanager plugin; https://github.com/smdahlen/vagrant-hostmanager
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true
```

The IP address (defined at `WORKBENCH_IP`) can be changed as needed.  The Vagrant `hostmanager` plugin is used to set up a simple hostname alias of *workbench*.

```
  ##################################################
  # Start Docker host
  ##################################################
  config.vm.define "workbench", autostart: true do |dh|  
    
    # configure virtual machine
    dh.vm.box = "dduportal/boot2docker"
    dh.vm.hostname = "workbench"
    dh.vm.network :private_network, ip: WORKBENCH_IP
    dh.hostmanager.aliases = %w(workbench.dev workbench)
    dh.vm.synced_folder ".", "/vagrant", type: "virtualbox"
```

The `dduportal/boot2docker` box is preferred as its more up to date than the default that comes with Vagrant.

We sync everything under the Workbench folder into the VM for convenience: `dh.vm.synced_folder ".", "/vagrant", type: "virtualbox"`

```
    dh.vm.provider "virtualbox" do |vb|
      vb.memory = 4096
      vb.cpus = 2
      vb.customize ["modifyvm", :id, "--nictype2", "virtio"]
    end

  end
```

The **Docker Host** is designed to hold the containers of several underlying projects, hence an initial setting of 4GB RAM and 2CPUs. These settings should be adjusted to suit your environment.

`vb.customize ["modifyvm", :id, "--nictype2", "virtio"]` is a network setting that seems to solve some problems for OSX users.  Feel free to remove or adjust to suit your environment.


```
  ##################################################
  # Provision Workbench
  ##################################################
  config.vm.provision "docker" do |d|
    d.images = ["tutum/mysql:5.6", "lucee/lucee-nginx"]
    # start nginx-proxy; https://github.com/jwilder/nginx-proxy
    d.run "jwilder/nginx-proxy", args: "-p 80:80 -v '/var/run/docker.sock:/tmp/docker.sock:ro'"
    # start dockerui; https://github.com/crosbymichael/dockerui
    d.run "dockerui/dockerui", args: "-p 81:9000 --privileged -v '/var/run/docker.sock:/var/run/docker.sock' -e VIRTUAL_HOST='workbench,workbench.dev'"
    # start cAdvisor; https://github.com/google/cadvisor
    d.run "google/cadvisor", args: "-v '/:/rootfs:ro' -v '/var/run:/var/run:rw' -v '/sys:/sys:ro' -v '/var/lib/docker/:/var/lib/docker:ro' -p 82:8080 --detach=true"
  end

# /config
end
```
The Workbench is provisioned by default with:

- common Docker images for development (feel free to adjust)
- an NGINX proxy set up to enable the use of hostnames with containers
- DockerUI for visualising the contents of your Boot2Docker instance (saves having to SSH onto the box and do things manually)
- cAdvisor for monitoring the health of the virtual machines containers

Enjoy.
