# Vagrantfile pour TP Vagrant : Server + ServerWorker K3s (version conforme au sujet + corrections réseau)

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"

  # ====== MACHINE SERVER ======
  config.vm.define "annieS" do |server|
    server.vm.hostname = "annieS"
    server.vm.network "private_network", ip: "192.168.56.110"

    server.vm.provider "virtualbox" do |vb|
      vb.name = "annieS"
      vb.memory = 1024
      vb.cpus = 1
    end

    # Provision pour installer K3s (contrôleur)
    server.vm.provision "shell", inline: <<-SHELL
      echo "=== Installation de K3s sur annieS (Server) ==="
      if ! command -v k3s &> /dev/null; then
        # Installation du contrôleur K3s avec IP fixe
        curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--node-ip=192.168.56.110" sh -
        echo "K3s installé avec succès sur annieS"
      else
        echo "K3s déjà installé sur annieS"
      fi

      # Copie du token K3s dans le dossier partagé
      sudo cat /var/lib/rancher/k3s/server/node-token > /vagrant/node-token.txt
      echo "Token sauvegardé dans /vagrant/node-token.txt"
    SHELL
  end

  # ====== MACHINE SERVERWORKER ======
  config.vm.define "annieSW" do |worker|
    worker.vm.hostname = "annieSW"
    worker.vm.network "private_network", ip: "192.168.56.111"

    worker.vm.provider "virtualbox" do |vb|
      vb.name = "annieSW"
      vb.memory = 1024
      vb.cpus = 1
    end

    # Provision pour installer K3s (agent)
    worker.vm.provision "shell", inline: <<-SHELL
      echo "=== Installation de K3s sur annieSW (Agent) ==="

      # Attente du token généré par le serveur
      while [ ! -f /vagrant/node-token.txt ]; do
        echo "En attente du token K3s du serveur..."
        sleep 5
      done

      TOKEN=$(cat /vagrant/node-token.txt)

      if ! command -v k3s &> /dev/null; then
        # Installation du worker avec IP fixe
        curl -sfL https://get.k3s.io | \
        K3S_URL=https://192.168.56.110:6443 \
        K3S_TOKEN=$TOKEN \
        INSTALL_K3S_EXEC="--node-ip=192.168.56.111" sh -
        echo "K3s installé avec succès sur annieSW"
      else
        echo "K3s déjà installé sur annieSW"
      fi
    SHELL
  end
end
