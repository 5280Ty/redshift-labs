# Planned Software Stack - RAM will be maxed on Nodes 1 & 3 as resources allow

## Node 1: Primary Node (Family Media & Cloud)
- **Model:** Dell OptiPlex 5050 SFF
- **CPU:** Intel i5-7500  (will upgrade to i7-7700 and/or replace machine)
- **RAM:** 16 GB DDR4 (good enough but will be maxed for critical I/O, caching, and database performance)
- **Storage:** 
  - OS SSD: 256 GB NVMe
  - Data SSD: 1 TB SATA (will synch with node 3 and later will backup to future NAS)
- **Hosted Applications:** 
  - Jellyfin – Media server (Installed)
  - MariaDB – Central database backend (Installed)
  - Vaultwarden – Secure password vault (Installed)(Inoperable)
  - Nextcloud – Personal cloud suite (Installed)
  - Portainer – Docker container management (Installed)
  - Homer - Server dashboard (Installed)
  - Immich – Photo/video management (post upgrade)
  - Audiobookshelf – Audiobooks & podcasts server
  - Calibre-Web – eBook management server
  - Samba – File sharing for scanning/media
  - Syncthing – Continuous file synchronization (Up with node 3)
- **Notes:** Max RAM will ensure smooth performance under heavy loads. Optiplex 5050 supports hardware acceleration (Intel QuickSync) reducing the CPU load for Jellyfin and Immich. Immich machine learning indexing to run off-hours. Nextcloud preview generator to be disabled. will migrate to a more powerful machine in the future. Synchthing between Node 1 and 3 provides redundancy for user data.

## Node 2: Network Access & Infrastructure Node
- **Model:** HP EliteDesk 705 G4 Mini
- **CPU:** Ryzen 5 Pro 2400GE
- **RAM:** 8 GB DDR4 (Will upgrade to 16 GB before Traefik install)
- **Storage:** 
  - OS SSD: 256 GB NVMe
  - Data SSD: 250 GB SATA (Downgraded)
- **Hosted Applications:** 
  - Ansible – Automation controller
  - Unbound – Recursive DNS resolver
  - AdGuard Home – DNS filtering
  - Prometheus – Metrics recording/storage
  - Watchtower – Container update automation
  - Grafana - Interactive Visualization
  - Traefik – Reverse proxy / SSL (added when network becomes public facing)
  - Open Canary - A decentralized network honeypot (to be added with Traefik with strict egress filtering)
- **Notes:** Will not get a RAM upgrade over 16 GB as that should be adequate for the stack with Traefik. Traefik will not be active until machine migration; the network is segmented; hardened; and is prepared to have internet facing applications.

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
- **Notes:** Headroom left for future expansion.

## SOC Node (Will be placed on a separate VLAN when possible)
- **Model:** HP ProDesk 600 G2 SFF
- **CPU:** Intel i5-6500 (Will Upgrade to i7-6700)  
- **RAM:** 40 GB DDR4 (Will max to 64 GB)
- **Storage:** 
  - OS SSD: 256 GB NVMe
  - Data HDD: 2 TB HDD
- **Hosted Applications:** 
  - Security Onion – SOC suite (Zeek, Wazuh, Suricata, Elasticsearch)
  - TheHive – Incident response management
  - Cortex – Automated analysis engine
  - Seek – Log correlation and search
- **Notes:** Isolated from production cluster; network capture handled via dedicated NIC.

## Lab Node
- **Model:** HP EliteDesk 800 G2 Mini (Will upgrade to i7-6700)
- **CPU:** Intel i5-6500T
- **RAM:** 16 GB DDR4 (Will upgrade to 32 GB)
- **Storage:** 
  - OS SSD: 512 GB NVMe
  - Data SSD: None
- **Hosted Applications:** 
  - DVWA - A Damn Vulnerable Web Application (Installed)
  - OWASP Juice Shop - An intentionally insecure web application (Installed)
  - WebGoat - A deliberately insecure web application (Installed)
  - Kali - A Linux penetration testing distribution
  - Windows Pro (Licensed) - Totally not Spyware
  - Windows Server - Offensive and Defensive VM
  - Cowrie - SSH and Telnet honeypot (to be added Post VLAN Segmentation)
- **Notes:** Offensive testing, malware sandboxing, and Red Team experimentation. Windows VMs will be off when not in use.

---

Environment: Project Nyxline Homelab  
Report: Software Stack Planned Deployment  
Author: Ty  
