# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "centos/7"

  config.vm.provider "virtualbox" do |v|
	v.gui = false
   v.customize ["modifyvm", :id, "--nic2", "bridged", "--bridgeadapter2", "enp7s0"]
   v.customize ["modifyvm", :id, "--nic3", "bridged", "--bridgeadapter3", "enp7s0"]
  end
  config.vm.provision "shell", inline: <<-SHELL 
	    #yum update -y
		yum install net-tools mtr -y

modprobe bonding

###Setings bonding

cat > /etc/sysconfig/network-scripts/ifcfg-bond0 << CMD
DEVICE=bond0
IPADDR=192.168.33.155
NETMASK=255.255.255.0
#GATEWAY=192.168.33.1
#DNS1=192.168.33.1
TYPE=Bond
ONBOOT=yes
BOOTPROTO=none
USERCTL=no
BONDING_OPTS="mode=1 miimon=100 fail_over_mac=1"
CMD

cat > /etc/sysconfig/network-scripts/ifcfg-eth1 << CMD
DEVICE=eth1
BOOTPROTO=none
ONBOOT=yes
MASTER=bond0
SLAVE=yes
USERCTL=no
CMD

cat > /etc/sysconfig/network-scripts/ifcfg-eth2 << CMD
DEVICE=eth2
BOOTPROTO=none
ONBOOT=yes
MASTER=bond0
SLAVE=yes
USERCTL=no
CMD
echo "alias bond0 bonding" > /etc/modprobe.d/bonding.conf
systemctl restart network.service
route 
ifdown eth1

# Settings cache only dns bind
yum install bind bind-utils -y

sed -i -e 's/\(127.0.0.1;\)/\1 any;/' -e 's/\(localhost;\)/\1 any;/' /etc/named.conf
sed -i '/allow-query /a  allow-query-cache       { localhost; any; };' /etc/named.conf


systemctl restart named
systemctl enable named
yum install bind-chroot -y
systemctl restart named
ln -s /etc/named.conf /var/named/chroot/etc/named.conf
echo 127.0.0.1 > /etc/resolv.conf
		  SHELL
end
