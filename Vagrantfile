# 
# Daemonite Docker Workbench
#
VAGRANTFILE_API_VERSION = "2"
WORKBENCH_IP = "172.22.22.22"
Vagrant.require_version ">= 1.7.3"

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
      vb.memory = 2048
      vb.customize ["modifyvm", :id, "--nictype2", "virtio"]
    end

  end

  ##################################################
  # Provision Workbench
  ##################################################
  # clean up containers prior to provisioning
  config.vm.provision "shell", inline: "docker rm --force `docker ps -qa`"
  # start nginx-proxy; https://github.com/jwilder/nginx-proxy
  config.vm.provision "shell", inline: "docker run -d -p 80:80 -v '/var/run/docker.sock:/tmp/docker.sock:ro' --name docker-proxy jwilder/nginx-proxy"
  # start dockerui; https://github.com/crosbymichael/dockerui
  config.vm.provision "shell", inline: "docker run -d -p 81:9000 --privileged -v '/var/run/docker.sock:/var/run/docker.sock' -e VIRTUAL_HOST='workbench,workbench.dev' --name dockerui dockerui/dockerui"
  # stock cache with common images
  config.vm.provision "shell", inline: "docker pull -a lucee/lucee-nginx"
  config.vm.provision "shell", inline: "docker pull tutum/mysql:5.6"

# /config
end