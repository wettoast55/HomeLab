VNet: management
192.168.50.0/24
├── Proxmox
├── TrueNAS
└── Router
 
VNet: services
192.168.60.0/24
├── Jellyfin
├── Immich
└── AMP
 
VNet: security
192.168.70.0/24
├── Zeek
├── Suricata
└── TryHackMe boxes


Network Traffic
↓
Suricata + Zeek?
↓
eve.json
↓
Wazuh Agent
↓
Wazuh Manager
↓
Dashboard
