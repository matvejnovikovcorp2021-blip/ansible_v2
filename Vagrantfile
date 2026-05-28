Vagrant.configure("2") do |config|
  config.vm.box = "debian/bullseye64"
  config.vm.box_check_update = false

  config.vm.synced_folder "./shared_folder", "/vagrant_shared", type: "virtualbox"

  config.vm.define "node1" do |node1|
    node1.vm.hostname = "node1"
    node1.vm.network "private_network", ip: "192.168.56.10"
    node1.vm.provider "virtualbox" do |vb|
      vb.name = "node1_debian"
      vb.memory = 512
      vb.cpus = 1
    end

    node1.vm.provision "install_python", type: "shell", before: :all, inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y python3 python3-pip
      echo "Python 3 and pip are ready on node1"
    SHELL

    node1.vm.provision "ansible_local" do |ansible|
      ansible.install_mode = "default"
      ansible.version = "latest"
      ansible.playbook = "playbook_node1.yml"
    end
  end

  config.vm.define "node2" do |node2|
    node2.vm.hostname = "node2"
    node2.vm.network "private_network", ip: "192.168.56.11"
    node2.vm.provider "virtualbox" do |vb|
      vb.name = "node2_debian"
      vb.memory = 512
      vb.cpus = 1
    end

    node2.vm.provision "install_python", type: "shell", before: :all, inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y python3 python3-pip
      echo "Python 3 and pip are ready on node2"
    SHELL

    node2.vm.provision "ansible_local" do |ansible|
      ansible.install_mode = "default"
      ansible.version = "latest"
      ansible.playbook = "playbook_node2.yml"
    end
  end
end