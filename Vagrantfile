# Vagrantfile
Vagrant.configure("2") do |config|
  config.vm.box = "oraclelinux/8"

  # --- etcd01 VM ---
  config.vm.define "etcd01" do |etcd|
    etcd.vm.hostname = "etcd01"
    etcd.vm.network "private_network", ip: "192.168.56.101"
    etcd.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = "1"
    end
    etcd.vm.provision "shell", inline: <<-SHELL
      useradd -m -G wheel ansible
      echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/ansible
      mkdir -p /home/ansible/.ssh
      chown -R ansible:ansible /home/ansible/.ssh
      echo "ansible:ansible" | chpasswd

      echo "192.168.56.101 etcd01" >> /etc/hosts
      echo "192.168.56.102 pgsql01" >> /etc/hosts
      echo "192.168.56.103 pgsql02" >> /etc/hosts
      echo "192.168.56.104 docker01" >> /etc/hosts
    SHELL
  end

  # --- pgsql01 VM ---
  config.vm.define "pgsql01" do |pgsql1|
    pgsql1.vm.hostname = "pgsql01"
    pgsql1.vm.network "private_network", ip: "192.168.56.102"
    pgsql1.vm.network "forwarded_port", guest: 5432, host: 5432, auto_correct: true
    pgsql1.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = "2"
    end
    pgsql1.vm.provision "shell", inline: <<-SHELL
      useradd -m -G wheel ansible
      echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/ansible
      mkdir -p /home/ansible/.ssh
      chown -R ansible:ansible /home/ansible/.ssh
      echo "ansible:ansible" | chpasswd

      echo "192.168.56.101 etcd01" >> /etc/hosts
      echo "192.168.56.102 pgsql01" >> /etc/hosts
      echo "192.168.56.103 pgsql02" >> /etc/hosts
      echo "192.168.56.104 docker01" >> /etc/hosts
    SHELL
  end

  # --- pgsql02 VM ---
  config.vm.define "pgsql02" do |pgsql2|
    pgsql2.vm.hostname = "pgsql02"
    pgsql2.vm.network "private_network", ip: "192.168.56.103"
    pgsql2.vm.network "forwarded_port", guest: 5432, host: 5433, auto_correct: true
    pgsql2.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = "2"
    end
    pgsql2.vm.provision "shell", inline: <<-SHELL
      useradd -m -G wheel ansible
      echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/ansible
      mkdir -p /home/ansible/.ssh
      chown -R ansible:ansible /home/ansible/.ssh
      echo "ansible:ansible" | chpasswd

      echo "192.168.56.101 etcd01" >> /etc/hosts
      echo "192.168.56.102 pgsql01" >> /etc/hosts
      echo "192.168.56.103 pgsql02" >> /etc/hosts
      echo "192.168.56.104 docker01" >> /etc/hosts
    SHELL
  end

  # --- docker01 VM ---
  config.vm.define "docker01" do |docker|
    docker.vm.hostname = "docker01"
    docker.vm.network "private_network", ip: "192.168.56.104"
    docker.vm.network "forwarded_port", guest: 9090, host: 9090, auto_correct: true
    docker.vm.network "forwarded_port", guest: 3000, host: 3000, auto_correct: true
    docker.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = "2"
    end

    docker.vm.provision "shell", inline: <<-SHELL
      useradd -m -G wheel ansible
      echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/ansible
      mkdir -p /home/ansible/.ssh
      chown -R ansible:ansible /home/ansible/.ssh
      echo "ansible:ansible" | chpasswd

      echo "192.168.56.101 etcd01" >> /etc/hosts
      echo "192.168.56.102 pgsql01" >> /etc/hosts
      echo "192.168.56.103 pgsql02" >> /etc/hosts
      echo "192.168.56.104 docker01" >> /etc/hosts

      sed -i 's/^PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
      systemctl restart sshd

      sudo -u ansible ssh-keygen -t rsa -f /home/ansible/.ssh/id_rsa -N ""

      for ip in 101 102 103; do
        ssh-keyscan 192.168.56.$ip >> /home/ansible/.ssh/known_hosts
        sudo -u ansible sshpass -p 'ansible' ssh-copy-id -f ansible@192.168.56.$ip
      done

      sed -i 's/^PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config
      systemctl restart sshd
    SHELL

    docker.vm.provision "shell", name: "Install base packages and Ansible", inline: <<-SHELL
      echo "DEBUG: Installing base tools and Ansible"
      dnf update -y
      dnf install -y oracle-epel-release-for-ol8 || dnf install -y epel-release || echo "Warning: EPEL not installed"
      dnf install -y python3-dnf python3-pip rsync gcc python3-devel rust cargo openssl-devel libffi-devel
      pip3 install --upgrade pip
      pip3 install setuptools_rust
      pip3 install ansible
    SHELL

    # Sync as vagrant, then chown + symlink for ansible
    docker.vm.synced_folder ".", "/home/vagrant/ansible_project", type: "rsync",
      rsync__exclude: ".vagrant/",
      rsync__args: ["--verbose", "--archive", "--delete"]

    docker.vm.provision "shell", run: "always", inline: <<-SHELL
      echo "Fixing ownership and linking ansible_project..."
      chown -R ansible:ansible /home/vagrant/ansible_project
      ln -sf /home/vagrant/ansible_project /home/ansible/ansible_project

      export PATH=$PATH:/usr/local/bin

      echo "Running Ansible playbook locally on docker01..."
      cd /home/ansible/ansible_project
      if [ -f inventory.ini ] && [ -f playbook.yml ]; then
        ansible-playbook -i inventory.ini playbook.yml || echo "Playbook failed or skipped"
      else
        echo "Ansible files not found in synced folder. Skipping execution."
      fi
    SHELL
  end
end
