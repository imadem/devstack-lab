# DevStack all-in-one with Vagrant and Shell provisioning


Based on https://github.com/lorin/devstack-vm, but since Ansible is not Windows friendly, I replaced it with Shell provisioning. 

Some settings don't survive reboot (intentionally, but that could change if needed):
  
    sudo ip link set dev eth2 up
    sudo ovs-vsctl add-port br-ex eth2
    sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE


  
  
