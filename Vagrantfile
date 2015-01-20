# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Use Ubuntu 14.04 64 bit
  config.vm.box = "ubuntu/trusty64"
  
  config.vm.hostname = "devstack"
  
  # Ubuntu has a "bug" (https://github.com/mitchellh/vagrant/issues/1673)
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"  

  # if you have a vagrant-cachier plugin, use it for apt caches
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

  # eth1, this will be the endpoint
  config.vm.network :private_network, ip: "192.168.27.100"
  # eth2, this will be the OpenStack "public" network, use DevStack default
  config.vm.network :private_network, ip: "172.24.4.225", :netmask => "255.255.255.224", :auto_config => false

  config.vm.provider :virtualbox do |vb|
  	# Disable headless mode, if you want to see the VBox console
  	#vb.gui = true
    
    # Set proper amount of RAM for a box, DevStack needs min. 1.5 GB if you install only essential OpenStack services
    vb.customize ["modifyvm", :id, "--memory", 2600]
	  # eth2 must be in promiscuous mode for floating IPs to be accessible
	  vb.customize ["modifyvm", :id, "--nicpromisc3", "allow-all"]
    
  end
   
  # change default US Ubuntu mirror to our local mirror at Arnes (Slovenia)
  config.vm.provision :shell, :inline =>  "sed -i 's!http\://us\.archive\.ubuntu\.com/ubuntu/!http\://ftp\.arnes\.si/pub/mirrors/ubuntu/!g;s!http\://security\.ubuntu\.com/ubuntu!http\://ftp\.arnes\.si/pub/mirrors/ubuntu!g' /etc/apt/sources.list"
  
  # install git
  config.vm.provision :shell, :inline =>  "apt-get update; apt-get -y install git"
  #install some packages, needed for standalone HEAT server (bug in install ./stack.sh script?)
  config.vm.provision :shell, :inline =>  "sudo apt-get -y install python-dev libxml2-dev libxslt1-dev libffi-dev"  

  #install gettext from apt, in 14.04 it is no longer installed with pyhon packets... (if you install heat, it has this by default)
  config.vm.provision :shell, :inline =>  "sudo apt-get -y install gettext"  

  # clone devstack as vagrant user (devstack branch should be in sync with the one in localrc)
  config.vm.provision :shell, :inline =>  "git clone -b stable/juno git://git.openstack.org/openstack-dev/devstack"
  # copy localrc to devstack directory, replace Windows CRLF line endings with Linux's LF, change ownership of devstack files
  config.vm.provision :shell, :inline =>  "cp /vagrant/localrc /home/vagrant/devstack/localrc; sed -i 's/\r$//' /home/vagrant/devstack/localrc; chown -R vagrant:vagrant devstack"  
  # enable eth2
  config.vm.provision :shell, :inline =>  "ip link set dev eth2 up"
  # run stack.sh as vagrant user
  config.vm.provision :shell, :inline => "cd devstack; sudo -u vagrant env HOME=/home/vagrant ./stack.sh"
  # add eth2 to br-ex
  config.vm.provision :shell, :inline => "ovs-vsctl add-port br-ex eth2"
  
end
