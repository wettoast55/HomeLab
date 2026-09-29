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
