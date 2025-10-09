# Vagrantfile pour TP Vagrant : Server + ServerWorker K3s

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
 
  # Dossier partagé (pour le token)
  config.vm.synced_folder ".", "/vagrant"
 
  # -------- MACHINE 1 : MASTER --------
  config.vm.define "annieS" do |server|
    server.vm.hostname = "annieS"
    server.vm.network "private_network", ip: "192.168.56.110"
 
    server.vm.provider "virtualbox" do |vb|
      vb.name = "annieS"
      vb.memory = 1024
      vb.cpus = 2
    end
 
    server.vm.provision "shell", inline: <<-SHELL
      echo "==== Installation du MASTER K3s ===="
      curl -sfL https://get.k3s.io | sh -
      echo "==== K3s installé sur annieS ===="
     
      # Copie le token dans le dossier partagé
      sudo cat /var/lib/rancher/k3s/server/node-token > /vagrant/node-token
      echo "==== Token K3s sauvegardé dans /vagrant/node-token ===="
    SHELL
  end
 
  # -------- MACHINE 2 : WORKER --------
  config.vm.define "annieSW" do |worker|
    worker.vm.hostname = "annieSW"
    worker.vm.network "private_network", ip: "192.168.56.111"
 
    worker.vm.provider "virtualbox" do |vb|
      vb.name = "annieSW"
      vb.memory = 1024
      vb.cpus = 2
    end
 
    worker.vm.provision "shell", inline: <<-SHELL
      echo "==== Installation du WORKER K3s ===="
      apt-get update -y
      apt-get install -y curl
 
      # Attente du token du master (max 2 min)
      echo "Attente du fichier /vagrant/node-token..."
      for i in {1..24}; do
        if [ -f /vagrant/node-token ]; then
          echo "Token trouvé !"
          break
        fi
        echo "En attente du token... ($i/24)"
        sleep 5
      done
 
      if [ ! -f /vagrant/node-token ]; then
        echo "❌ Token non trouvé après 2 minutes. Abandon."
        exit 1
      fi
 
      TOKEN=$(cat /vagrant/node-token)
      K3S_URL="https://192.168.56.110:6443"
 
      echo "Connexion du worker au cluster..."
      curl -sfL https://get.k3s.io | K3S_URL=$K3S_URL K3S_TOKEN=$TOKEN sh -
 
      echo "==== Worker connecté avec succès ===="
    SHELL
  end
end
 