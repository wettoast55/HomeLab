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

###wazuh single node deployment###
#create workspace, download wazuh docker packages
cd /opt
git clone https://github.com/wazuh/wazuh-docker.git
cd wazuh-docker
git fetch --all --tags
git tag -l | grep 4.14

#pick stable tag and download
git checkout v4.14.6
docker compose pull

#run
docker compose -f generate-indexer-certs.yml run --rm generator
docker compose up -d

#if permission error check ct is priviledged in proxmox
"Error response from daemon: failed to create task for container: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: error during container init: error setting rlimits for ready process: error setting rlimit type 7: operation not permitted"

#exit ct and in proxmox, show ct priviledges
root@pve:~# pct config 104
arch: amd64
cores: 4
features: nesting=1,keyctl=1
hostname: wazuh
memory: 8192
nameserver: 1.1.1.1
net0: name=eth0,bridge=vmbr0,hwaddr=BC:24:11:48:7B:CC,ip=dhcp,type=veth
ostype: ubuntu
rootfs: local-lvm:vm-104-disk-0,size=100G
swap: 2048
unprivileged: 1

pct stop 104
nano /etc/pve/lxc/104.conf

#check to ensure these changes are made then restart and enter ct to retry command
unprivileged: 0
features: nesting=1,keyctl=1
lxc.apparmor.profile: unconfined
lxc.cap.drop:

#restart and enter and retry
pct start 104
pct enter 104
cd /opt/wazuh-docker/single-node
docker compose up -d

#show container ip and open in browser
ip -4 addr show eth0 

