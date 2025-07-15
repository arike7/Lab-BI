Vagrant.configure("2") do |config|

  # Define a common box for all VMs
  config.vm.box = "oraclelinux/8"
  config.vm.box_url = "https://oracle.github.io/vagrant-projects/boxes/oraclelinux/8.json"
  
  # --- etcd01 VM ---
  config.vm.define "etcd01" do |etcd|
    etcd.vm.hostname = "etcd01"
    etcd.vm.network "private_network", ip: "192.168.56.101" # Private IP for internal communication
    etcd.vm.provider "virtualbox" do |vb|
      vb.memory = "1024" # 1 GiB for ETCD
      vb.cpus = "1"
    end
  end

  # --- pgsql01 VM (Primary PostgreSQL instance) ---
  config.vm.define "pgsql01" do |pgsql1|
    pgsql1.vm.hostname = "pgsql01"
    pgsql1.vm.network "private_network", ip: "192.168.56.102"
    pgsql1.vm.provider "virtualbox" do |vb|
      vb.memory = "2048" # 2 GiB for PostgreSQL
      vb.cpus = "2"
    end
    pgsql1.vm.network "forwarded_port", guest: 5432, host: 5432, auto_correct: true
  end

  # --- pgsql02 VM (Secondary PostgreSQL instance) ---
  config.vm.define "pgsql02" do |pgsql2|
    pgsql2.vm.hostname = "pgsql02"
    pgsql2.vm.network "private_network", ip: "192.168.56.103"
    pgsql2.vm.provider "virtualbox" do |vb|
      vb.memory = "2048" # 2 GiB for PostgreSQL
      vb.cpus = "2"
    end
    pgsql2.vm.network "forwarded_port", guest: 5432, host: 5433, auto_correct: true
  end

  # --- docker01 VM ---
  config.vm.define "docker01" do |docker|
    docker.vm.hostname = "docker01"
    docker.vm.network "private_network", ip: "192.168.56.104"
    docker.vm.provider "virtualbox" do |vb|
      vb.memory = "2048" # 2 GiB for Docker and monitoring
      vb.cpus = "2"
    end
    docker.vm.network "forwarded_port", guest: 9090, host: 9090, auto_correct: true
    docker.vm.network "forwarded_port", guest: 3000, host: 3000, auto_correct: true
    # Install Python and Ansible on docker01
    docker.vm.provision "shell", inline: <<-SHELL
      sudo dnf install -y oracle-epel-release-for-ol8
      sudo dnf install -y python3-dnf python3-pip
      sudo pip3 install ansible
    SHELL
  end

  # --- Manual Ansible Execution from docker01 ---
  config.vm.provision "shell", run: "always", inline: <<-SHELL
    echo "Attempting to run Ansible playbook from docker01..."
    # Copy Ansible files (playbook, inventory, roles) to docker01
    rsync -avz --exclude '.vagrant/' /vagrant/ /home/vagrant/ansible_project/

    # Connect to docker01 and execute ansible-playbook
    # We first run setup_ansible_user_and_keys.yml using the 'vagrant' user and its key,
    # then run the main playbook using the new 'ansible' user (as defined in inventory.ini).
    ssh -i ~/.vagrant.d/insecure_private_key -o StrictHostKeyChecking=no vagrant@192.168.56.104 << 'EOF'
      cd /home/vagrant/ansible_project/

      echo "Running initial setup_ansible_user_and_keys.yml with vagrant user..."
      # This first playbook connects as 'vagrant' and uses Vagrant's default SSH key
      ansible-playbook -i inventory.ini setup_ansible_user_and_keys.yml -vvv --user vagrant --private-key /home/vagrant/.ssh/id_rsa

      echo "Running main playbook.yml with ansible user..."
      # After setup_ansible_user_and_keys.yml creates the 'ansible' user and distributes keys,
      # the main playbook can be run. It will use the 'ansible' user and key from inventory.ini.
      ansible-playbook -i inventory.ini playbook.yml -vvv
EOF
    echo "Ansible playbook execution from docker01 completed."
  SHELL

end