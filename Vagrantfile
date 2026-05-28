Vagrant.configure("2") do |config|
  config.vm.box = "debian/bullseye64"
  config.vm.box_check_update = false

  config.vm.synced_folder "./shared_folder", "/vagrant_shared", type: "virtualbox"

  # Фикс сети и переключение на HTTP
  config.vm.provision "fix_network", type: "shell", run: "always", inline: <<-SHELL
    echo "nameserver 8.8.8.8" > /etc/resolv.conf
    echo "nameserver 8.8.4.4" >> /etc/resolv.conf
    sed -i 's|https://|http://|g' /etc/apt/sources.list
    apt-get update
    echo "Network fix completed"
  SHELL

  config.vm.define "node1" do |node1|
    node1.vm.hostname = "node1"
    node1.vm.network "private_network", ip: "192.168.56.15"
    node1.vm.provider "virtualbox" do |vb|
      vb.name = "node1_debian"
      vb.memory = 512
      vb.cpus = 1
    end

    node1.vm.provision "install_ansible", type: "shell", inline: <<-SHELL
      if ! command -v ansible &> /dev/null; then
        echo "Installing Ansible on node1..."
        apt-get install -y ansible
      else
        echo "Ansible already installed on node1"
      fi
    SHELL

    node1.vm.provision "ansible_local" do |ansible|
      ansible.install = false
      ansible.playbook = "playbook_common.yml"
      ansible.limit = "all"
      ansible.compatibility_mode = "2.0"
    end
  end

  config.vm.define "node2" do |node2|
    node2.vm.hostname = "node2"
    node2.vm.network "private_network", ip: "192.168.56.16"
    node2.vm.provider "virtualbox" do |vb|
      vb.name = "node2_debian"
      vb.memory = 512
      vb.cpus = 1
    end

    node2.vm.provision "install_ansible", type: "shell", inline: <<-SHELL
      while fuser /var/lib/apt/lists/lock >/dev/null 2>&1; do
        echo "Waiting for apt lock to be released..."
        sleep 5
      done
      if ! command -v ansible &> /dev/null; then
        echo "Installing Ansible on node2..."
        apt-get install -y ansible
      else
        echo "Ansible already installed on node2"
      fi
    SHELL

    node2.vm.provision "ansible_local" do |ansible|
      ansible.install = false
      ansible.playbook = "playbook_common.yml"
      ansible.limit = "all"
      ansible.compatibility_mode = "2.0"
    end
  end
end