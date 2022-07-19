
#Crate a vairable with the script to update repo, install mysql and create a user 
$script_mysql = <<-SCRIPT
  apt-get update && \
  apt-get install -y mysql-server-5.7 && \
  mysql -e "create user 'phpuser'@'%' identified by 'pass';"
SCRIPT

Vagrant.configure("2") do |config|

  config.vm.provider "virtualbox" do |vb|   
    vb.customize [ "modifyvm", :id, "--uartmode1", "disconnected" ]  
  end

  config.vm.box = "ubuntu/bionic64"
  config.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd;", run: "always"
  config.vm.synced_folder '.', '/home/vagrant/bionic'

  #Port fowarding:
  config.vm.network "forwarded_port", guest: 80, host: 8089

  #Static IP configuration
  #config.vm.network "private_network", ip: "192.168.56.4"

  #DHCP configuration
  #config.vm.network "private_network", type: "dhcp"

  #Public IP configuration
  config.vm.network "public_network", ip: "192.168.15.3"

  #set public key to the server  
  config.vm.provision "shell", 
    inline: "cat /configs/id_bionic.pub >> .ssh/authorized_keys"

  # call the script to install mysql
  config.vm.provision "shell", inline: $script_mysql

  # change the mysql config file to open connector from anywhere
  config.vm.provision "shell", 
    inline: "cat /configs/mysqld.cnf > /etc/mysql/mysql.conf.d/mysqld.cnf"

  # restart the mysql to recognize changes
  config.vm.provision "shell", 
    inline: "service mysql restart"

  
  #creating syncronized folder
  config.vm.synced_folder "./.configs", "/configs"

  #disable default syncronized folder
  config.vm.synced_folder ".", "/bionic", disabled: true

end
