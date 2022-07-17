* Info

- To fix error using wsl I've include the next configurations to Vagrantfile:

```bash
config.vm.provider "virtualbox" do |vb|   
    vb.customize [ "modifyvm", :id, "--uartmode1", "disconnected" ]  
end

config.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd;", run: "always"
config.vm.synced_folder '.', '/home/vagrant/bionic'
```