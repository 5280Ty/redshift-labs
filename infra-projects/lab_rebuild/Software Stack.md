# Homelab Stack 2.1 (In Progress)
## Node 1: New Primary (Family Media & Cloud) (Purchased) 

- **Model:** Dell OptiPlex 7050 Micro
- **CPU:** Intel i7-7700
- **RAM:** 32 GB DDR4
- **Storage:** 
  - OS SSD: 256 GB NVMe
  - Data SSD: 1 TB SATA (will migrate SSD from previous Primary Node)
- **Hosted Applications:** 
  - Jellyfin – Media server (active)
  - MariaDB – Central database backend (active)
  - Vaultwarden – Secure password vault (installed, offline until traefik)
  - Nextcloud – Personal cloud suite (active)
  - Portainer – Docker container management (active)
  - Homer - Server dashboard (active)
  - BookLore - eBook management serve (active)
- **Planned Future Applications**
  - Audiobookshelf - Audiobooks & podcasts server (coming soon)
  - Monica - Personal CRM (Coming Soon)
  - Heimdall - Guest Dashboard (coming Soon)
  - Mealie – Recipe manager (coming soon)
  - CUPS – Scanner interface only (coming soon)
  - Immich – Photo/video management (post Quik Sync)
  - Samba – File sharing for scanning/media (Up with Node 3 and Syncthing)
  - Syncthing – Continuous file synchronization (Up with node 3)
  - Home Assistant - Automation platform (Up Last on this Node)
- **Notes:** Max RAM (32 GB) will ensure smooth performance under heavy loads. Optiplex 7050 supports hardware acceleration (Intel Quick Sync) reducing the CPU load for Jellyfin and Immich. Immich machine learning indexing to run off-hours. Nextcloud preview generator to be disabled. Synchthing between Node 1 and 3 provides redundancy for user data and Samba(?).

## Node 2: Infrastructure Node (Active)
- **Model:** HP EliteDesk 705 G4 Mini
- **CPU:** Ryzen 5 Pro 2400GE
- **RAM:** 12 GB DDR4 (16 GB RAM upgrade ordered)
- **Storage:** 
  - OS SSD: 256 GB NVMe
  - Data SSD: 250 GB SATA
- **Hosted Applications:** 
  - Ansible – Automation controller (Up last on this cluster)
  - Unbound – Recursive DNS resolver (down Unconfigured)
  - AdGuard Home – DNS filtering (down Unconfigured)
  - Prometheus – Metrics recording/storage (active)
  - Watchtower – Container update automation (Up)
  - Grafana - Interactive Visualization (active)
- **Planned Future Applications**
  - Traefik – Reverse proxy / SSL (added when network becomes public facing)
- **Notes:** Will not get a RAM upgrade over 16 GB as that should be adequate for the stack with Traefik. Traefik will not be active until machine migration; the network is segmented; hardened; and is prepared to have internet facing applications.

## Node 3: Backup / Observability Node (Active)
- **Model:** HP EliteDesk 705 G4 Mini
- **CPU:** Ryzen 5 Pro 2400GE
- **RAM:** 16 GB DDR4
- **Storage:**  - Open Canary - A decentralized network honeypot (to be added with Traefik with strict egress filtering)
  - OS SSD: 256 GB NVMe
  - Data SSD: 1 TB SATA
- **Hosted Applications:** 
  - Promtail – Log collection
  - Loki – Centralized log aggregation
  - Proxmox Backup Server – Cluster backup coordination
  - Samba – File sharing for scanning/media
  - Syncthing – Synchronization target
  - Uptime Kuma – Service uptime monitoring
- **Notes:** To be optimized for log aggregation, backup, and library serving. 16 GB RAM is adequate, any bottlenecks would likely be the CPU.

## Lab Node (Active)
- **Model:** HP EliteDesk 800 G2 Mini (Will upgrade to i7-6700)
- **CPU:** Intel i5-6500T
- **RAM:** 32 GB DDR4 (max) (configured 2133)
- **Storage:** 
  - OS SSD: 512 GB NVMe
  - Data SSD: None
- **Hosted Applications:** 
  - DVWA - A Damn Vulnerable Web Application (active)
  - OWASP Juice Shop - An intentionally insecure web application (sctive)
  - WebGoat - A deliberately insecure web application (active)
  - CyberChef- Cyber Swiss Army Knife (will become backup post SOC deployment)
  - Kali Purple - A Linux penetration testing distribution (active)
- **Planned Future Applications**
  - Windows Pro (Licensed) - Totally not Spyware (planned)
  - Windows Server - Offensive and Defensive VM (planned)
- **Notes:** Offensive testing, malware sandboxing, and Red Team experimentation. Windows VMs will be off when not in use to conserve resources. Upgrading CPU is a priority.

# Planned Future Nodes

## pfSense Node (Former Node 1) (awaiting NIC)
- **Model:** Dell OptiPlex 5050 SFF
- **CPU:** Intel i5-7500 
- **RAM:** 16 GB DDR4 (Reduced from 24 GB)
- **Storage:** 
  - OS SSD: 256 GB NVMe
  - Data SSD: None
- **Hosted Applications:** 
  - pfsense
- **Notes:** Nothing else will run on a pfsense node. Additional services increase the attack surface (unacceptable for a firewall). This also avoids performance spikes starving packet processing. Dual NIC card to be added for future expandability (DMZ?).


## SOC Node (Will be placed on a separate VLAN when possible)
- **Model:** HP ProDesk 600 G2 SFF
- **CPU:** Intel i5-6500 (Will Upgrade to i7-6700)  
- **RAM:** 64 GB DDR4 (max)
- **Storage:** 
  - OS SSD: 256 GB NVMecontroller
  - Data HDD: 2 TB HDD
- **Hosted Applications:** 
  - Security Onion – SOC suite (Zeek, Wazuh, Suricata, Elasticsearch)
  - TheHive – Incident response management
  - Cortex – Automated analysis engine
- **Planned Future Applications**
  - Seek – Log correlation and search
- **Notes:** Up Last. CPU upgrade critical. Isolated from production cluster; network capture handled via dedicated NIC.

---

# Homelab Stack 2.0 Plan (Original)

## Node 1: Primary Node (Family Media & Cloud) 
- **Model:** Dell OptiPlex 5050 SFF
- **CPU:** Intel i5-7500  (will upgrade to i7-7700 and/or replace machine)
- **RAM:** 24 GB DDR4 (upgraded but may be maxed for critical I/O, caching, and database performance)
- **Storage:** 
  - OS SSD: 256 GB NVMe
  - Data SSD: 1 TB SATA (will backup to future NAS)
- **Hosted Applications:** 
  - Jellyfin – Media server (active)
  - MariaDB – Central database backend (active)
  - Vaultwarden – Secure password vault (Down until traefik)
  - Nextcloud – Personal cloud suite (active)
  - Portainer – Docker container management (active)
  - Homer - Server dashboard (active)
- **Planned Future Applications**
  - Immich – Photo/video management (post upgrade)
  - Audiobookshelf - Audiobooks & podcasts server (post upgrade)
  - Calibre - eBook management server (post upgrade)
  - Samba – File sharing for scanning/media (Up with Node 3)
  - Syncthing – Continuous file synchronization (Up with node 3)
- **Notes:** Max RAM will ensure smooth performance under heavy loads. Optiplex 5050 supports hardware acceleration (Intel QuickSync) reducing the CPU load for Jellyfin and Immich. Immich machine learning indexing to run off-hours. Nextcloud preview generator to be disabled. will migrate to a more powerful machine in the future. Synchthing between Node 1 and 3 provides redundancy for user data.

## Node 2: Network Access & Infrastructure Node
- **Model:** HP EliteDesk 705 G4 Mini
- **CPU:** Ryzen 5 Pro 2400GE
- **RAM:** 12 GB DDR4 (May upgrade to 16 GB before Traefik install)
- **Storage:** 
  - OS SSD: 256 GB NVMe
  - Data SSD: 250 GB SATA
- **Hosted Applications:** 
  - Ansible – Automation controller (planned)
  - Unbound – Recursive DNS resolver (down Unconfigured)
  - AdGuard Home – DNS filtering (down Unconfigured)
  - Prometheus – Metrics recording/storage (active)
  - Watchtower – Container update automation (Up)
  - Grafana - Interactive Visualization (active)
- **Planned Future Applications**
  - Traefik – Reverse proxy / SSL (added when network becomes public facing)
  - Open Canary - A decentralized network honeypot (to be added with Traefik with strict egress filtering)
- **Notes:** Will not get a RAM upgrade over 16 GB as that should be adequate for the stack with Traefik. Traefik will not be active until machine migration; the network is segmented; hardened; and is prepared to have internet facing applications.


## SOC Node (Will be placed on a separate VLAN when possible)
- **Model:** HP ProDesk 600 G2 SFF
- **CPU:** Intel i5-6500 (Will Upgrade to i7-6700)  
- **RAM:** 64 GB DDR4 (max)
- **Storage:** 
  - OS SSD: 256 GB NVMecontroller
  - Data HDD: 2 TB HDD
- **Hosted Applications:** 
  - Security Onion – SOC suite (Zeek, Wazuh, Suricata, Elasticsearch)
  - TheHive – Incident response management
  - Cortex – Automated analysis engine
- **Planned Future Applications**
  - Seek – Log correlation and search
- **Notes:** Isolated from production cluster; network capture handled via dedicated NIC.

## Lab Node (Will be placed on a separate VLAN when possible)
- **Model:** HP EliteDesk 800 G2 Mini (Will upgrade to i7-6700)
- **CPU:** Intel i5-6500T
- **RAM:** 32 GB DDR4 (max) (configured 2133)
- **Storage:** 
  - OS SSD: 512 GB NVMe
  - Data SSD: None
- **Hosted Applications:** 
  - DVWA - A Damn Vulnerable Web Application (active)
  - OWASP Juice Shop - An intentionally insecure web application (sctive)
  - WebGoat - A deliberately insecure web application (active)
  - CyberChef- Cyber Swiss Army Knife (will become backup post SOC deployment)
  - Kali Purple - A Linux penetration testing distribution (active)
  - Windows Pro (Licensed) - Totally not Spyware (planned)
  - Windows Server - Offensive and Defensive VM (planned)
- **Planned Future Applications**
  - Cowrie - SSH and Telnet honeypot (to be added Post VLAN Segmentation)
- **Notes:** Offensive testing, malware sandboxing, and Red Team experimentation. Windows VMs will be off when not in use to conserve ram.

# Planned Future Nodes

## Node 3: System Metrics, File Synch, & Backups Node
- **Model:** HP EliteDesk 705 G4 Mini
- **CPU:** Ryzen 5 Pro 2400GE
- **RAM:** 16 GB DDR4
- **Storage:** 
  - OS SSD: 256 GB NVMe
  - Data SSD: 1 TB SATA
- **Hosted Applications:** 
  - Promtail – Log collection
  - Loki – Centralized log aggregation
  - Proxmox Backup Server – Cluster backup coordination
  - Samba – File sharing for scanning/media
  - Syncthing – Synchronization target
  - Uptime Kuma – Service uptime monitoring
- **Notes:** To be optimized for log aggregation, backup, and library serving. 16 GB RAM is adequate, any bottlenecks would likely be the CPU.

## Node 4: Home Automation & Aux Services Node
- **Model:** HP EliteDesk 705 G4 Mini
- **CPU:** Ryzen 5 Pro 2400GE
- **RAM:** 16 GB DDR4
- **Storage:** 
  - OS SSD: 1 TB NVMe
  - Data SSD: 1 TB SATA
- **Hosted Applications:** 
  - Home Assistant – Automation platform
  - Mealie – Recipe manager
  - CUPS – Scanner interface only
  - Monica - Personal CRM
- **Notes:** Headroom left for future expansion.

---

Environment: Project Nyxline Homelab  
Report: Software Stack Planned Deployment  
Author: Ty  
