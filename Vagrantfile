# Vagrantfile pour TP Vagrant : Server + ServerWorker

Vagrant.configure("2") do |config|
  # Box de base pour toutes les machines
  config.vm.box = "ubuntu/bionic64"   # Remplace par la version qu'on veut

  # 1️ Machine Server (CalixteS)
 
  config.vm.define "AnnieS" do |server|
    server.vm.hostname = "AnnieS"
    server.vm.network "private_network", ip: "192.168.56.110"

    # Provider VirtualBox : ressources minimales
    server.vm.provider "virtualbox" do |vb|
      vb.name = "AnnieS"
      vb.memory = 1024
      vb.cpus = 2
    end

    # Provisioning shell (bonus K3s en mode server)
    server.vm.provision "shell", inline: <<-SHELL
      echo "Provisioning Server (AnnieS)..."
      # Installer K3s en mode server 
      curl -sfL https://get.k3s.io | sh -
      echo "K3s server installé sur AnnieS"
    SHELL
  end

  # 2️ Machine ServerWorker (CalixteSW)
  
  config.vm.define "AnnieSW" do |worker|
    worker.vm.hostname = "AnnieSW"
    worker.vm.network "private_network", ip: "192.168.56.111"

    # Provider VirtualBox : ressources minimales
    worker.vm.provider "virtualbox" do |vb|
      vb.name = "AnnieSW"
      vb.memory = 1024
      vb.cpus = 2
    end

    # Provisioning shell (bonus K3s en mode agent)
    worker.vm.provision "shell", inline: <<-SHELL
      echo "Provisioning ServerWorker (AnnieSW)..."
      # Récupération du token K3s depuis le serveur
      K3S_TOKEN=$(ssh -o StrictHostKeyChecking=no vagrant@192.168.56.110 "sudo cat /var/lib/rancher/k3s/server/node-token")
      # Installer K3s en mode agent (décommenter si nécessaire)
      curl -sfL https://get.k3s.io | K3S_URL=https://192.168.56.110:6443 K3S_TOKEN=YOUR_TOKEN sh -
      echo "K3s worker installé et rejoint le cluster"
    SHELL
  end
end
