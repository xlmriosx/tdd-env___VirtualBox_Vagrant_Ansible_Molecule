Vagrant.configure("2") do |config|
    config.vm.provider "virtualbox"
    config.vm.box = "alvistack/ubuntu-23.10"
    config.vm.hostname = "tdd-env"

    config.vm.define "vm" do |vm|
        vm.vm.network "private_network", ip: "10.0.0.100"
    end

    # Configurating keys to enter server
    config.vm.provision "file", source: "~/.ssh", destination: "/home/vagrant/.ssh"
    config.vm.provision "shell", inline: "chmod 700 -R /home/vagrant/.ssh"
    
    # Mount repo to development
    config.vm.synced_folder ".", "/home/vagrant/ansible"

    # Installing Ansible, Molecule
    config.vm.provision "shell", inline: <<-SHELL
        sudo usermod -aG sudo vagrant
        sudo chmod -R g+rwx /var
        # Actualiza todos los paquetes
        apt upgrade -y && apt update -y
        # Agregate el repo de podman
        sh -c "echo 'deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_20.04/ /' > /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list"
        # Firmale nae porque sino apt le desconfia
        wget -nv https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable/xUbuntu_20.04/Release.key -O- | sudo apt-key add -
        # Actualiza la lista de repos de apt
        apt upgrade -y && sudo apt update -y
        # Instala podman
        apt install podman -y
        # Agregate el repo de ansible
        apt-add-repository ppa:ansible/ansible
        # Instalate ansible y las herramientas de desarrollo
        apt install ansible pipx libssl-dev ansible-lint -y
        pipx install "molecule[lint]"
        pipx install "podman"
        pipx install "molecule[podman]"
        pipx install "ansible-lint"
    SHELL
end
