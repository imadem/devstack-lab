# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Use Ubuntu 12.04 64 bit
  config.vm.box = "precise64"
  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  # Ubuntu 12.04 has a "bug" (https://github.com/mitchellh/vagrant/issues/1673)
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"  

  # eth1, this will be the endpoint
  config.vm.network :private_network, ip: "192.168.27.100"
  # eth2, this will be the OpenStack "public" network, use DevStack default
  config.vm.network :private_network, ip: "172.24.4.225", :netmask => "255.255.255.224", :auto_config => false
  config.vm.provider :virtualbox do |vb|
  	# Disable headless mode, if you want to see the VBox console
  	#vb.gui = true
  
    # Set proper amount of RAM for a box, DevStack needs min. 1.5 GB
	vb.customize ["modifyvm", :id, "--memory", 2048]
	# eth2 must be in promiscuous mode for floating IPs to be accessible
	vb.customize ["modifyvm", :id, "--nicpromisc3", "allow-all"]
  end
   
  # install git
  config.vm.provision :shell, :inline =>  "apt-get -y install git"
  # clone devstack as vagrant user
  config.vm.provision :shell, :inline =>  "git clone -b stable/havana git://git.openstack.org/openstack-dev/devstack"
  # copy localrc to devstack directory, change ownership of devstack files
  config.vm.provision :shell, :inline =>  "cp /vagrant/localrc /home/vagrant/devstack/localrc; chown -R vagrant:vagrant devstack"  
  # enable eth2
  config.vm.provision :shell, :inline =>  "ip link set dev eth2 up"
  # run stack.sh as vagrant user
  config.vm.provision :shell, :inline => "cd devstack; sudo -u vagrant env HOME=/home/vagrant ./stack.sh"
  # add eth2 to br-ex
  config.vm.provision :shell, :inline => "ovs-vsctl add-port br-ex eth2"
  # Allow VMs to connect to the internet - configures NAT on eth0 adapter
  config.vm.provision :shell, :inline => "iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE"

end
