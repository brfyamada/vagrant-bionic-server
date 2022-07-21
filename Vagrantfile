
#Crate a vairable with the script to update repo, install mysql and create a user 
$script_mysql = <<-SCRIPT
  apt-get update && \
  apt-get install -y mysql-server-5.7 && \
  mysql -e "create user 'phpuser'@'%' identified by 'pass';"
SCRIPT

Vagrant.configure("2") do |config|

  config.vm.provider "virtualbox" do |vb|   
    vb.customize ["modifyvm", :id, "--uart1", "0x3F8", "4"]
    vb.customize [ "modifyvm", :id, "--uartmode1", "disconnected" ]  
  end

  config.vm.box = "ubuntu/bionic64"

  # config.vm.define "mysqldb" do |mysql|
    
  #   mysql.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd;", run: "always"
  #   mysql.vm.synced_folder '.', '/home/vagrant/bionic'

  #   #Static IP configuration
  #   mysql.vm.network "private_network", ip: "192.168.56.4"

  #   #DHCP configuration
  #   #mysql.vm.network "private_network", type: "dhcp"

  #   #Public IP configuration
  #   #mysql.vm.network "public_network", ip: "192.168.15.3"

  #   #set public key to the server  
  #   mysql.vm.provision "shell", 
  #     inline: "cat /configs/id_bionic.pub >> .ssh/authorized_keys"

  #   # call the script to install mysql
  #   mysql.vm.provision "shell", inline: $script_mysql

  #   # change the mysql config file to open connector from anywhere
  #   mysql.vm.provision "shell", 
  #     inline: "cat /configuration/mysqld.cnf > /etc/mysql/mysql.conf.d/mysqld.cnf"

  #   # restart the mysql to recognize changes
  #   mysql.vm.provision "shell", 
  #     inline: "service mysql restart"

    
  #   #creating syncronized folder
  #   mysql.vm.synced_folder "./.configs", "/configs"

  #   #disable default syncronized folder
  #   mysql.vm.synced_folder ".", "/vagrant", disabled: true
  # end

  config.vm.define "phpweb" do |phpweb|

    phpweb.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication no/g' /etc/ssh/sshd_config; sudo systemctl restart sshd;", run: "always"
    phpweb.vm.synced_folder '.', '/home/vagrant/bionic'
    #Port fowarding:
    phpweb.vm.network "forwarded_port", guest: 8888, host: 8888

    #Static IP configuration
    #phpweb.vm.network "private_network", ip: "192.168.56.5"
    phpweb.vm.network "public_network", ip: "192.168.15.25"

    phpweb.vm.provision "shell", 
      inline: "apt-get update && apt-get install -y puppet"

    phpweb.vm.provision "puppet" do |puppet|
      puppet.manifests_path = "manifests"
      puppet.manifest_file = "phpweb.pp"
    end
  end

  config.vm.define "mysqlserver" do |mysqlserver|

    mysqlserver.vm.provider "virtualbox" do |vbm|   
      vbm.memory = 1024
      vbm.cpus = 2
      vbm.name = "ubuntu_bionic_mysql"
    end


    mysqlserver.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication no/g' /etc/ssh/sshd_config; sudo systemctl restart sshd;", run: "always"
    mysqlserver.vm.synced_folder '.', '/home/vagrant/bionic'
    #mysqlserver.vm.network "private_network", ip: "192.168.56.4"
    mysqlserver.vm.network "public_network", ip: "192.168.15.24"
    mysqlserver.vm.provision "shell", inline: "cat ./bionic/.configs/id_bionic.pub >> .ssh/authorized_keys"

  end

  config.vm.define "ansible" do |ansible|

    ansible.vm.provider "virtualbox" do |vba|   
      vba.memory = 512
      vba.cpus = 1
      vba.name = "ubuntu_bionic_ansible3"
    end

    ansible.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication no/g' /etc/ssh/sshd_config; sudo systemctl restart sshd;", run: "always"
    ansible.vm.synced_folder '.', '/home/vagrant/bionic'
    #ansible.vm.network "private_network", ip: "192.168.56.7"
    ansible.vm.network "public_network", ip: "192.168.15.26"
    

    ansible.vm.provision "shell",
      inline: "sudo apt-get update && \
              sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 93C4A3FD7BB9C367 && \
              sudo apt-get update && \
              sudo apt-get install -y software-properties-common && \
              sudo apt-add-repository 'deb http://ppa.launchpad.net/ansible/ansible-2.8/ubuntu bionic main' && \
              apt-get update && \
              sudo apt-get install -y ansible"

    ansible.vm.provision "shell",
      inline: "cp ./bionic/.local/id_bionic /home/vagrant/id_bionic && \
                chmod 600 /home/vagrant/id_bionic"

    ansible.vm.provision "shell",
      inline: "ansible-playbook -i ./bionic/ansible/hosts \
                ./bionic/ansible/playbook.yml"

  end

end
