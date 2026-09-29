09/29/2026

Reseaching vlans and attempting to set up security zones

"""
cat /etc/network/interfaces
"""

vlan not yet aware

If vmbr0 does not contain:
"""
bridge-vlan-aware yes
"""

add to:
"""
auto vmbr0
iface vmbr0 inet static
    address 192.168.50.2/24
    gateway 192.168.50.1
    bridge-ports nic0
    bridge-stp off
    bridge-fd 0
    bridge-vlan-aware yes
"""

#reload networking
root@pve:~# ifreload -a

#check vlan staus, bridge is functioning as a normal, untagged network, And bridge vlan show confirms everything is on VLAN 1:

root@pve:~# ip -br link
lo               UNKNOWN        00:00:00:00:00:00 <LOOPBACK,UP,LOWER_UP> 
nic0             UP             cc:28:aa:dd:a6:38 <BROADCAST,MULTICAST,UP,LOWER_UP> 
tailscale0       UNKNOWN        <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> 
vmbr0            UP             cc:28:aa:dd:a6:38 <BROADCAST,MULTICAST,UP,LOWER_UP> 
tap100i0         UNKNOWN        92:d3:3a:4f:95:55 <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> 
fwbr100i0        UP             2a:80:87:18:a2:22 <BROADCAST,MULTICAST,UP,LOWER_UP> 
fwpr100p0@fwln100i0 UP             92:38:2f:b7:f7:1d <BROADCAST,MULTICAST,UP,LOWER_UP> 
fwln100i0@fwpr100p0 UP             2a:80:87:18:a2:22 <BROADCAST,MULTICAST,UP,LOWER_UP> 
tap102i0         UNKNOWN        16:26:d5:1e:93:bb <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> 
fwbr102i0        UP             12:6a:64:42:8d:59 <BROADCAST,MULTICAST,UP,LOWER_UP> 
fwpr102p0@fwln102i0 UP             ee:19:df:bb:15:2f <BROADCAST,MULTICAST,UP,LOWER_UP> 
fwln102i0@fwpr102p0 UP             12:6a:64:42:8d:59 <BROADCAST,MULTICAST,UP,LOWER_UP> 

root@pve:~# bridge vlan show
port              vlan-id  
nic0              1 PVID Egress Untagged
vmbr0             1 PVID Egress Untagged
tap100i0          1 PVID Egress Untagged
fwbr100i0         1 PVID Egress Untagged
fwpr100p0         1 PVID Egress Untagged
fwln100i0         1 PVID Egress Untagged
tap102i0          1 PVID Egress Untagged
fwbr102i0         1 PVID Egress Untagged
fwpr102p0         1 PVID Egress Untagged
fwln102i0         1 PVID Egress Untagged
root@pve:~# 
