# Vagrantfile pour TP Vagrant : Server + ServerWorker

Vagrant.configure("2") do |config|
  # Box de base pour toutes les machines
  config.vm.box = "ubuntu/bionic64"   # Remplace par la version que tu veux

  # 1️ Machine Server (nomS)
 
  config.vm.define "nomS" do |server|
    server.vm.hostname = "nomS"
    server.vm.network "private_network", ip: "192.168.56.110"

    # Provider VirtualBox : ressources minimales
    server.vm.provider "virtualbox" do |vb|
      vb.name = "nomS"
      vb.memory = 512
      vb.cpus = 1
    end

    # Provisioning shell (bonus K3s en mode server)
    server.vm.provision "shell", inline: <<-SHELL
      echo "Provisioning Server (nomS)..."
      # Installer K3s en mode server (décommenter si nécessaire)
      # curl -sfL https://get.k3s.io | sh -
    SHELL
  end

  # 2️ Machine ServerWorker (nomSW)
  
  config.vm.define "nomSW" do |worker|
    worker.vm.hostname = "nomSW"
    worker.vm.network "private_network", ip: "192.168.56.111"

    # Provider VirtualBox : ressources minimales
    worker.vm.provider "virtualbox" do |vb|
      vb.name = "nomSW"
      vb.memory = 512
      vb.cpus = 1
    end

    # Provisioning shell (bonus K3s en mode agent)
    worker.vm.provision "shell", inline: <<-SHELL
      echo "Provisioning ServerWorker (nomSW)..."
      # Installer K3s en mode agent (décommenter si nécessaire)
      # curl -sfL https://get.k3s.io | K3S_URL=https://192.168.56.110:6443 K3S_TOKEN=YOUR_TOKEN sh -
    SHELL
  end
end
