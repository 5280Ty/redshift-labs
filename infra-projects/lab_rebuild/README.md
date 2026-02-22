<p align="center">
<img src="https://github.com/5280Ty/5280Ty/blob/main/lab.png" alt="Lab" width="700"/>
</p>

<h1 align="center">Lab Rebuild: New City, Better Lab</h1>

**Date:** 2025-10-06  
**System Role:** A compact, multiple system research and training environment that combines system administration, network defense, and offensive security within a single controlled ecosystem.  

---

## Objective  
To build and manage a fully developed homelab that serves as a research and training environment for developing and strengthening skills in system administration, network configuration, security, and problem solving across diverse technologies.  

---

## Systems Overview  

### - Network and Peripherals
- TP Link TL-SG108E 8-Port Gigabit Managed Smart Switch
- Netgear GS308E 8-Port Gigabit Managed Smart Switch
- HP Printer
- LG TV

---

<img src="https://github.com/5280Ty/5280Ty/blob/main/dash.png" alt="Lab" width="700"/>

### Planned Software Stack

#### Node 1: Primary (Family Media & Cloud) (Active)
- **Model:** Dell OptiPlex 5050 SFF
- **CPU:** Intel i5-7500   
- **RAM:** 16 GB DDR4 (MAX 64), 2 X 8 GB@2400 MT/s (2400 configured speed)
- **Storage** 256 GB NVMe/1 TB SATA

|Hosted Applications|CPU Load|Typical RAM Use|Role|
|---|---|---|---|
|Jellyfin|High|1-4 GB|Media Server|
|MariaDB|Medium|.2-2 GB|Central database backend|
|Vaultwarden|Low|.05-1 GB|Secure password vault|
|Nextcloud|Medium|.2-2 GB|Personal cloud suite|
|Portainer|Low|50 - 500 MB|Docker container management|
|Homer|Low|50-200 MB|Server dashboard|
|Audiobookshelf|Medium|.5-2GB|Audiobooks & podcasts server|
|Calibre-Web|Low|0.5-1 GB|eBook management server|
|Samba|Low|.5-1 GB|File sharing for scanning/media|
|Syncthing|Medium|.1-2 GB|Continuous file synchronization|
|Immich|High|2-6 GB|Photo/video management|
|Total|High|8-24 GB|-|

---

#### Node 2: Network Access & Infrastructure (Active)

- **Model:** HP EliteDesk 705 G4 Mini
- **CPU:** Ryzen 5 Pro 2400GE
- **RAM:** 12 GB DDR4 (MAX 32@2933), 1 X 8 GB@2400 MT/s, 1 X 4 GB@2667 MT/s (2400 configured speed)
- **Storage** 256 GB NVMe/250 GB SATA

|Hosted Applications|CPU Load|Typical RAM Use|Role|
|---|---|---|---|
|AdGuard Home|Low|50-300 MB|DNS filtering|
|Ansible|High|100-500 MB|Automation controller|
|Grafana|Low/Mod|.02-1 GB|Interactive Visualization|
|Prometheus|Moderate|1-4 GB|Metrics recording/storage|
|Unbound|Low|20-200 MB|Recursive DNS resolver|
|Watchtower|Very Low|50-150 MB|Container update automation|
|Traefik|Moderate|50-200 MB|Reverse proxy / SSL|
|Open Canary|Very Low|50-150 MB|A network honeypot|
|Total|Moderate|4-12 GB|-|

---

#### Node 3: System Metrics, File Synch, & Backups (Coming Soon)

- **Model:** HP EliteDesk 705 G4 Mini
- **CPU:** Ryzen 5 Pro 2400GE
- **RAM:** 16 GB DDR4 (MAX 32@2933), 1 X@16 2667 MHz 
- **Storage** 256 GB NVMe/1 TB SATA

|Hosted Applications|CPU Load|Typical RAM Use|Role|
|---|---|---|---|
|Loki|Moderate|.05-2|Centralized log aggregation|
|Promtail|Low|100-300 MB|Log collection|
|Proxmox Backup Server|Mod/High|1-4+ GB|Cluster backup coordination|
|Samba|Low/Mod|0.1-1 GB|File sharing for scanning/media|
|Syncthing|Mod/High|0.1-1 GB|Synchronization target|
|Uptime Kuma|Very Low|50-150 MB|Service uptime monitoring|
|Total|Moderate|8-12 GB|-|

---

#### Node 4: Home Automation & Aux Services Node (Planned)
- **Model:** Dell Optiplex 7050 Micro
- **CPU:** i7-7700T
- **RAM:** 8 GB DDR4 (MAX 32@2400), 
- **Storage**  256 GB NVMe/ -  

|Hosted Applications|CPU Load|Typical RAM Use|Role|
|---|---|---|---|
|CUPS|Very Low|50-100 MB|Scanner interface|
|Home Assistant|Moderate|0.5-2 GB|Automation platform|
|Mealie|Low/Mod|200-500 MB|Recipe manager|
|PiHole|Low|.2-1|Backup DNS|
|Total|Low|4-8 GB|-|

---

#### SOC Node (BlackAegis)  (Coming Soon)
- **Model:** HP ProDesk 600 G2 SFF  
- **CPU:** Intel i5-6500   
- **RAM:** 64 GB DDR4 (MAX), 4 X 16@3200 MHz
- **Storage:** 256? GB NVMe/1 TB SSD

|Hosted Applications|CPU Load|Typical RAM Use|Role|
|---|---|---|---|
|Cortex|High|1-4|Automated analysis engine|
|Security Onion|Very High|16-32 GB (rec)|SOC suite|
|Seek|Mod/High|100-300 MB|Log correlation and search|
|TheHive|Mod/High|1-8 GB (rec)|Incident response management|
|Total|High|40+ GB|-|

---
<img src="https://github.com/5280Ty/5280Ty/blob/main/dash2.png" alt="Lab" width="700"/>

#### Lab Node (Redshift) (Active)
- **Model:** HP EliteDesk 800 G2 Mini  
- **CPU:** Intel i5-6500T  
- **RAM:** 16 GB DDR4 (MAX 32@3200), 2 X 8 GB@2133 MHz (2133 configured speed)  
- **Storage:** 512 GB NVMe  

|Hosted Applications|CPU Load|Typical RAM Use|Role|
|---|---|---|---|
|DVWA|Very Low|50-150|A Damn Vulnerable Web Application|
|OWASP Juice Shop|Very Low|.05- 2 GB|An Insecure web application|
|WebGoat|Low/Mod|.5-1 GB|Another insecure web application|
|WPVuln|Low|.5-2|WordPress vuln web app|
|CyberChef|Low/Mod|.2-1|Cyber Multi-Tool|
|Kali Purple|Mod/High|6-10+ GB|A Linux penetration testing distribution VM|
|Windows Pro|Mod/High|4-8+ GB|Offensive and Defensive VM|
|Windows Server|Mod/High|8-16+ GB|AD, OU, GPO, and Kerberos management and testing|
|Total|Moderate/High|20-40 (32) GB|-|

---

## Planned Labs / Projects
Vulnerability Lab with multiple VM's:Kali Linux, Win client, Juice Shop, DVWA, Metasploitable, and other target VM's.

---

