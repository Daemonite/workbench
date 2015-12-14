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
  
  # setting that appears to help provisioning on Windows
  config.ssh.forward_agent = true

  ##################################################
  # Start Docker host
  ##################################################
  config.vm.define "workbench", autostart: true do |dh|  
    
    # configure virtual machine
    dh.vm.box = "dduportal/boot2docker"
    dh.vm.box_check_update = false
    dh.vm.box_version = "1.8.3"
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
  # clean up containers prior to provisioning
  config.vm.provision "shell", inline: <<-SHELL
    # clean up containers prior to provisioning
    echo "Removing all containers (running, dead, or otherwise)"
    docker rm --force `docker ps -qa`
    echo "Pruning dangling images"
    docker rmi $(docker images -qa -f "dangling=true")
    echo "Soaping.. Scrubbing.. Spongeing.. Docker cleaned!"
  SHELL

  config.vm.provision "docker" do |d|
    d.images = ["tutum/mysql:5.6", "lucee/lucee4-nginx"]
    # start nginx-proxy; https://github.com/jwilder/nginx-proxy
    d.run "daemonite/workbench-proxy", image: "daemonite/workbench-proxy:latest", args: "-p 80:80 -v '/var/run/docker.sock:/tmp/docker.sock:ro'"
    # default host displaying all running virtuals; http://workbench
    d.run "texthtml/docker-vhosts", image: "texthtml/docker-vhosts", args: "-e VIRTUAL_HOST='workbench,workbench.dev' -v '/var/run/docker.sock:/tmp/docker.sock:ro'"
    # start dockerui; https://github.com/crosbymichael/dockerui
    d.run "dockerui/dockerui", args: "-p 81:9000 --privileged -v '/var/run/docker.sock:/var/run/docker.sock'"
    # start cAdvisor; https://github.com/google/cadvisor
    d.run "google/cadvisor", args: "-v '/:/rootfs:ro' -v '/var/run:/var/run:rw' -v '/sys:/sys:ro' -v '/var/lib/docker/:/var/lib/docker:ro' -p 82:8080 --detach=true"
  end

# /config
end
