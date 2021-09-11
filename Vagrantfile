Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/focal64"
  
  public_key = File.read("id_rsa.pub")
  config.vm.define "node1" do |machine|
    machine.vm.network "private_network", ip: "172.17.177.21", virtualbox__intnet: true
	machine.vm.hostname = "node1"
	machine.vm.provision "file", source: "id_rsa", destination: "/home/vagrant/.ssh/id_rsa"
    machine.vm.provision :shell, :inline =>"
     echo 'Copying ansible-vm public SSH Keys to the VM'
     mkdir -p /home/vagrant/.ssh
     chmod 700 /home/vagrant/.ssh
     echo '#{public_key}' >> /home/vagrant/.ssh/authorized_keys
     chmod -R 600 /home/vagrant/.ssh/authorized_keys
     echo 'Host 192.168.*.*' >> /home/vagrant/.ssh/config
     echo 'StrictHostKeyChecking no' >> /home/vagrant/.ssh/config
     echo 'UserKnownHostsFile /dev/null' >> /home/vagrant/.ssh/config
     chmod -R 600 /home/vagrant/.ssh/config
	 chmod 600 /home/vagrant/.ssh/id_rsa
     ", privileged: false	
  end

  config.vm.define "node2" do |machine|
    machine.vm.network "private_network", ip: "172.17.177.22", virtualbox__intnet: true
    machine.vm.hostname = "node2"
	machine.vm.provision "file", source: "id_rsa", destination: "/home/vagrant/.ssh/id_rsa"
    machine.vm.provision :shell, :inline =>"
     echo 'Copying ansible-vm public SSH Keys to the VM'
     mkdir -p /home/vagrant/.ssh
     chmod 700 /home/vagrant/.ssh
     echo '#{public_key}' >> /home/vagrant/.ssh/authorized_keys
     chmod -R 600 /home/vagrant/.ssh/authorized_keys
     echo 'Host 192.168.*.*' >> /home/vagrant/.ssh/config
     echo 'StrictHostKeyChecking no' >> /home/vagrant/.ssh/config
     echo 'UserKnownHostsFile /dev/null' >> /home/vagrant/.ssh/config
     chmod -R 600 /home/vagrant/.ssh/config
	 chmod 600 /home/vagrant/.ssh/id_rsa
     ", privileged: false		
  end

  config.vm.define 'controller' do |machine|
    machine.vm.network "private_network", ip: "172.17.177.11", virtualbox__intnet: true
	machine.vm.hostname = "controller"
	machine.vm.provision "file", source: "id_rsa", destination: "/home/vagrant/.ssh/id_rsa"
    machine.vm.provision :shell, :inline =>"
     echo 'Copying ansible-vm public SSH Keys to the VM'
     mkdir -p /home/vagrant/.ssh
     chmod 700 /home/vagrant/.ssh
     echo '#{public_key}' >> /home/vagrant/.ssh/authorized_keys
     chmod -R 600 /home/vagrant/.ssh/authorized_keys
     echo 'Host 192.168.*.*' >> /home/vagrant/.ssh/config
     echo 'StrictHostKeyChecking no' >> /home/vagrant/.ssh/config
     echo 'UserKnownHostsFile /dev/null' >> /home/vagrant/.ssh/config
     chmod -R 600 /home/vagrant/.ssh/config
	 chmod 600 /home/vagrant/.ssh/id_rsa
     ", privileged: false		
    machine.vm.synced_folder "./", "/vagrant",  
	          owner: "vagrant", 
			  group: "vagrant",
			  mount_options: ["dmode=775,fmode=600"]
    machine.vm.provision :ansible_local do |ansible|
      ansible.playbook       = "cri-containerd.yml"
	  ansible.become = true
	  ansible.config_file="/vagrant/conf/ansible.cfg"
	  # "vvv" for more verbose
      ansible.verbose        =  true
      ansible.install        = true
	  ansible.limit          = "all"
	  ansible.inventory_path = "inventory"
    end
  end

end
