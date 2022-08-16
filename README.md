# Info

- Run the next commands to initialize the vagrant instance:
```bash
vagrant up
```

- To connect to instance, obs: if you are using windows terminal you've to install the client ssh if not installed yet: 
```bash
vagrant ssh
```

- The use the user and pass as "vagrant" to login the server.
- To fix errors using vagrant with wsl I've include the next configurations to Vagrantfile:

```bash
config.vm.provider "virtualbox" do |vb|   
    vb.customize [ "modifyvm", :id, "--uartmode1", "disconnected" ]  
end

config.vm.provision :shell, :inline => "sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config; sudo systemctl restart sshd;", run: "always"
config.vm.synced_folder '.', '/home/vagrant/bionic'
```

- I had to use virtualbox installed in windows using wsl and install vagrant in linux. 

# Config pubic_key and private_key 

- Create 2 folders .configs and .local in the root path of project

- Generate the prublic_key and private_key 
```bash
ssh-keygen -t rsa
```

- name the key as "id_bionic"

- put "id_bionic.pub" inside .configs and "id_bionic" inside .local

configur

## links used to solve problems:
-  Permission denied (publickey) : https://stackoverflow.com/questions/50271150/vagrant-permission-denied-publickey#:~:text=To%20fix%20the%20Permission%20denied,authorised_keys%20file%20on%20the%20guest.
- (VERR_FILE_NOT_FOUND) https://superuser.com/questions/1622167/vagrant-box-fails-trying-to-log-to-a-path-the-box-was-created-from/1622179#1622179
- (VERR_FILE_NOT_FOUND) https://github.com/leandog/atdd-linux-vagrant-vm/issues/2
- WSL config: https://blog.thenets.org/how-to-run-vagrant-on-wsl-2/ (THIS WILL SOLVE PROBLEM)