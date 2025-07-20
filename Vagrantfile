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
      dnf install -y python3
      useradd -m -s /bin/bash ansible
      mkdir -p /home/ansible/.ssh
      echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/ansible
      echo 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDFv1AE+CK3IgqEO0+0jBgf7O3FmAM0/Gt9gc7+mVdJsJtxjh8LBQ5fyMn0MDO6plfjgE5bHbTH+zZzgqGgMKtwrnR8fk/+kCkTggCHldQVdS+TvQkQ9sP2LdfkACj49OwSDQPgB2j7qWaj+0bOHWGsfbMjZNka8Kh325mrhDBKCGwqWWiogTDR1ktknLH8YNlP/0gwzN7KCRpf16ePf5KNM/ydXqtEe+F/js/3bziyp2ofsF3eo9rTTDEoYwkAwop/QnzdvTuVp/ZQRvvb1d4ALOTZJ5YaEzr61KnoYbZAnyo4xmrm2USh2OsoVrm2P9uaCD/cXveSJehyV8xkpIxqr2AhENasni72SJ87tvMxc/3Q/wwLd2HbZ1dGUJ3UgNswkrWZ397I2Mdg3QVWNtoU2degQ6SWJ2jg8oLUelgdOyk0AlCP9CZvs+RB6SrwuLif69vxLBxz8znnJMt9ZoFkWP5lbbtztGqVa+usd0/uioJFgAZibTpijwi3+E/36i0= ansible@docker01' > /home/ansible/.ssh/authorized_keys
      chown -R ansible:ansible /home/ansible/.ssh
      chmod 700 /home/ansible/.ssh
      chmod 600 /home/ansible/.ssh/authorized_keys

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
      dnf install -y python3
      useradd -m -s /bin/bash ansible
      mkdir -p /home/ansible/.ssh
      echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/ansible
      echo 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDFv1AE+CK3IgqEO0+0jBgf7O3FmAM0/Gt9gc7+mVdJsJtxjh8LBQ5fyMn0MDO6plfjgE5bHbTH+zZzgqGgMKtwrnR8fk/+kCkTggCHldQVdS+TvQkQ9sP2LdfkACj49OwSDQPgB2j7qWaj+0bOHWGsfbMjZNka8Kh325mrhDBKCGwqWWiogTDR1ktknLH8YNlP/0gwzN7KCRpf16ePf5KNM/ydXqtEe+F/js/3bziyp2ofsF3eo9rTTDEoYwkAwop/QnzdvTuVp/ZQRvvb1d4ALOTZJ5YaEzr61KnoYbZAnyo4xmrm2USh2OsoVrm2P9uaCD/cXveSJehyV8xkpIxqr2AhENasni72SJ87tvMxc/3Q/wwLd2HbZ1dGUJ3UgNswkrWZ397I2Mdg3QVWNtoU2degQ6SWJ2jg8oLUelgdOyk0AlCP9CZvs+RB6SrwuLif69vxLBxz8znnJMt9ZoFkWP5lbbtztGqVa+usd0/uioJFgAZibTpijwi3+E/36i0= ansible@docker01' > /home/ansible/.ssh/authorized_keys
      chown -R ansible:ansible /home/ansible/.ssh
      chmod 700 /home/ansible/.ssh
      chmod 600 /home/ansible/.ssh/authorized_keys

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
      dnf install -y python3
      useradd -m -s /bin/bash ansible
      mkdir -p /home/ansible/.ssh
      echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/ansible
      echo 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDFv1AE+CK3IgqEO0+0jBgf7O3FmAM0/Gt9gc7+mVdJsJtxjh8LBQ5fyMn0MDO6plfjgE5bHbTH+zZzgqGgMKtwrnR8fk/+kCkTggCHldQVdS+TvQkQ9sP2LdfkACj49OwSDQPgB2j7qWaj+0bOHWGsfbMjZNka8Kh325mrhDBKCGwqWWiogTDR1ktknLH8YNlP/0gwzN7KCRpf16ePf5KNM/ydXqtEe+F/js/3bziyp2ofsF3eo9rTTDEoYwkAwop/QnzdvTuVp/ZQRvvb1d4ALOTZJ5YaEzr61KnoYbZAnyo4xmrm2USh2OsoVrm2P9uaCD/cXveSJehyV8xkpIxqr2AhENasni72SJ87tvMxc/3Q/wwLd2HbZ1dGUJ3UgNswkrWZ397I2Mdg3QVWNtoU2degQ6SWJ2jg8oLUelgdOyk0AlCP9CZvs+RB6SrwuLif69vxLBxz8znnJMt9ZoFkWP5lbbtztGqVa+usd0/uioJFgAZibTpijwi3+E/36i0= ansible@docker01' > /home/ansible/.ssh/authorized_keys
      chown -R ansible:ansible /home/ansible/.ssh
      chmod 700 /home/ansible/.ssh
      chmod 600 /home/ansible/.ssh/authorized_keys

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
      dnf install -y python3 python3-pip vim git
      echo "Creating ansible user and SSH directory"
      useradd -m -s /bin/bash ansible
      echo "ansible ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/ansible
      mkdir -p /home/ansible/.ssh
      chown -R ansible:ansible /home/ansible/.ssh
      echo "192.168.56.101 etcd01" >> /etc/hosts
      echo "192.168.56.102 pgsql01" >> /etc/hosts
      echo "192.168.56.103 pgsql02" >> /etc/hosts
      echo "192.168.56.104 docker01" >> /etc/hosts
    SHELL
    
    # Установка Ansible
    docker.vm.provision "shell", name: "Install base packages and Ansible", inline: <<-SHELL
      echo "DEBUG: Installing base tools and Ansible"
      dnf update -y
      dnf install -y oracle-epel-release-for-ol8 || dnf install -y epel-release || echo "Warning: EPEL not installed"
      dnf install -y python3-dnf python3-pip rsync gcc python3-devel rust cargo openssl-devel libffi-devel
      pip3 install --upgrade pip
      pip3 install setuptools_rust
      pip3 install ansible
    SHELL

    # Sync project folder
    docker.vm.synced_folder ".", "/home/vagrant/ansible_project", type: "rsync",
      rsync__exclude: ".vagrant/",
      rsync__args: ["--verbose", "--archive", "--delete"]

    # Копирование приватного ключа вручную из проекта
    docker.vm.provision "shell", inline: <<-SHELL
      echo "Copying private key manually"
      cp /home/vagrant/ansible_project/id_rsa /home/ansible/.ssh/id_rsa
      #cp -r /home/vagrant/ansible_project /home/ansible/
      chown ansible:ansible /home/ansible/.ssh/id_rsa
      chmod 600 /home/ansible/.ssh/id_rsa
    SHELL

    # PATH to Ansible
    docker.vm.provision "shell", inline: <<-SHELL
      echo 'export PATH=$PATH:/usr/local/bin' >> /home/ansible/.bashrc
      chown ansible:ansible /home/ansible/.bashrc
    SHELL

    # Запуск Ansible от пользователя ansible
    docker.vm.provision "shell", run: "always", inline: <<-SHELL
      echo "Fixing permissions and ownership..."
      chmod o+x /home/vagrant
      chown -R ansible:ansible /home/vagrant/ansible_project
      echo "Linking ansible_project..."
      ln -sf /home/vagrant/ansible_project /home/ansible/ansible_project
      chown -R ansible:ansible /home/ansible/ansible_project
      echo "Running Ansible playbook as ansible user..."
      sudo -u ansible bash -c '
        cd /home/ansible/ansible_project
        echo "Running Ansible playbook locally on docker01..."
        if [ -f inventory.ini ] && [ -f playbook.yml ]; then
          /usr/local/bin/ansible-playbook -i inventory.ini playbook.yml --vault-password-file vault_pass || echo "Playbook failed or skipped"
        else
          echo "Ansible files not found in synced folder. Skipping execution."
        fi
      '
    SHELL
  end
end

    

    

    
