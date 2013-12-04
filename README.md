# DevStack all-in-one with Vagrant and Shell provisioning


Based on https://github.com/lorin/devstack-vm, but since Ansible is not Windows friendly (my students use Windows almost exclusively on their laptops), I replaced it with Shell provisioning. I used Ubuntu 12.04 LTS instead of Ubuntu 13.10.

Some settings don't survive reboot (intentionally, change that if you need a more permanent solution):
  
    sudo ip link set dev eth2 up
    sudo ovs-vsctl add-port br-ex eth2
    sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

Network configuration in VM:

* eth0 - NAT network to your computer (host)
	* Vagrant uses this for SSH access (`vagrant ssh`) - port forwarding from localhost's port 2222 to VM's port 22
* eth1 - this will be the endpoint for Horizon and other OpenStack APIs (192.168.27.100)
* eth2 - in promiscuous mode, this will be the OpenStack "public" network, use DevStack default 172.24.4.225/27


After install:

	Horizon is now available at http://192.168.27.100/
	Keystone is serving at http://192.168.27.100:5000/v2.0/
	Examples on using novaclient command line is in exercise.sh
	The default users are: admin and demo
	The password: password
	This is your host ip: 192.168.27.100

