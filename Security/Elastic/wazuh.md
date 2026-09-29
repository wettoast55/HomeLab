### CT CREATION ###
#downloading ct template to node:
    pveam update
    pveam download local ubuntu-24.04-standard_24.04-2_amd64.tar.zst

#verify ct is present in storage:
    pveam list local

#create container using ct template:
pct create 104 local:vztmpl/ubuntu-24.04-standard_24.04-2_amd64.tar.zst \
  --hostname wazuh \
  --cores 4 \
  --memory 8192 \
  --swap 2048 \
  --rootfs local-lvm:100 \
  --net0 name=eth0,bridge=vmbr0,ip=dhcp \
  --features nesting=1,keyctl=1 \
  --unprivileged 1

#start and enter ct from pve:
pct start 104
pct enter 104

### DOCKER INSTALLATION ###
#install docker using ubuntushell from pve/pct 104:
apt update && apt upgrade -y
apt install -y curl wget nano
 
apt install -y curl wget nano ca-certificates
curl -fsSL https://get.docker.com | sh
 
apt install -y docker-compose-plugin

#verify docker install
docker --version
docker compose version
--------------------------

### fixing misconfigured dns due to tailscale ###
### If 8.8.8.8 fails → networking problem.
###If 8.8.8.8 works but google.com fails → DNS problem.
###If both fail → network configuration problem.###

#commands show what correctly pings, google.com github.com
ip a
 
ping -c 4 8.8.8.8
ping -c 4 github.com

ip addr
ip route
cat /etc/resolv.conf
ping -c 4 8.8.8.8
ping -c 4 google.com

#On the Proxmox host:
pct stop 104
pct set 104 --nameserver 1.1.1.1
pct start 104

### while creating ct dns was set based on tailscale connection, so dns was 100.100.100.100, changed to 1.1.1.1 and retested.
root@wazuh:~# cat /etc/resolv.conf 
# --- BEGIN PVE ---
search tail87924c.ts.net
nameserver 1.1.1.1
# --- END PVE ---

#can successfully run docker install
apt update
apt upgrade -y
apt install -y curl wget nano



