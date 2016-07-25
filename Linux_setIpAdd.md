Linux Notes: Set IP Addresses
---------------------------

Set static IP addresses

### Ubuntu

File to edit: `/etc/network/interfaces`

Add the following lines
```
iface eth0 inet static
address 192.168.1.110
netmask 255.255.0.0
gateway 0.0.0.0
```

### CentOS 6.4
##### Configure static IP address
File to edit: `/etc/sysconfig/network-scripts/ifcfg-eth11` (Replace `eth11` with network adapter name)

Add the following lines
```
DEVICE=eth11
BOOTPROTO=none
ONBOOT=yes
IPADDR=192.168.1.110
NETMASK=255.255.0.0
NETWORK=0.0.0.0
```

##### Configure DNS Server
File to edit: `/etc/resolv.conf`

```
nameserver 8.8.8.8      # Replace with your nameserver ip
nameserver 192.168.1.1  # Replace with your nameserver ip
```

### VSphere iHesxiV
Top right hand corner, the two computers icon beside the user name (i.e. `super`), beside the date. Right click > Edit Connections > Wired eth11 > Edit > IPv4 Settings

### Restart Network interface
Works for both CentOS and Ubuntu
`sudo /etc/init.d/network restart`
