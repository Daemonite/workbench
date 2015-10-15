# 
# Daemonite Docker Workbench
#
VAGRANTFILE_API_VERSION = "2"
WORKBENCH_IP = "172.22.22.22"
Vagrant.require_version ">= 1.7.4"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # configure hostmanager plugin; https://github.com/smdahlen/vagrant-hostmanager
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true
  

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

    dh.vm.provider "virtualbox" do |vb|
      vb.memory = 4096
      vb.cpus = 2
      vb.customize ["modifyvm", :id, "--nictype2", "virtio"]
    end

  end


  ##################################################
  # Provision Workbench
  ##################################################
  config.vm.provision "shell", inline: <<-SHELL
    # clean up containers prior to provisioning
    docker rm --force `docker ps -qa`
    # start nginx-proxy; https://github.com/jwilder/nginx-proxy
    docker run -d -p 80:80 -v '/var/run/docker.sock:/tmp/docker.sock:ro' --name docker-proxy jwilder/nginx-proxy
    # start dockerui; https://github.com/crosbymichael/dockerui
    docker run -d -p 81:9000 --privileged -v '/var/run/docker.sock:/var/run/docker.sock' -e VIRTUAL_HOST='workbench,workbench.dev' --name dockerui dockerui/dockerui
    # start cAdvisor; https://github.com/google/cadvisor
    docker run -v '/:/rootfs:ro' -v '/var/run:/var/run:rw' -v '/sys:/sys:ro' -v '/var/lib/docker/:/var/lib/docker:ro' -p 82:8080 --detach=true --name=cadvisor google/cadvisor:latest
    # stock cache with common images
    docker pull -a lucee/lucee-nginx
    docker pull tutum/mysql:5.6
  SHELL

# /config
end