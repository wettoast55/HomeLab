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

#install docker using ubuntushell from pve/pct 104:
apt update && apt upgrade -y
apt install -y curl wget nano
 
curl -fsSL https://get.docker.com | sh
apt install -y docker-compose-plugin
