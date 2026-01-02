# Day 6 - A True Quorum

Author: Ty  
Date: December 2025  
Environment: Project Nyxline Home Lab  

---

## Objective

To bring a third Proxmox node online that will establish quorum and to expand observability. Primary goals were storage expansion, Loki-based logging, uptime monitoring, and service growth on NoctHaven.

---

## Node 3 Build and Proxmox Setup
Hardware: HP EliteDesk 705 G4 Mini
Changes:
- Added 256 GB NVMe SSD
- Added 1 TB SATA SSD
- Installed Proxmox VE
- Followed the internal Nyxline [Proxmox Start Guide](https://github.com/5280Ty/redshift-labs/blob/main/infra-projects/lab_rebuild/Proxmox_Start_Guide.md) for baseline configuration.

---

## Observability Stack Deployment (Node 3)

```bash
mkdir -p \
  /srv/loki/data \
  /srv/loki/config \
  /srv/promtail/config \
  /srv/uptime-kuma/data

chmod -R 755 /srv
```

Docker Compose (Loki, Promtail, Uptime Kuma)

```yml
services:
  loki:
    image: grafana/loki:3.0.0
    container_name: loki
    restart: unless-stopped
    ports:
      - "3100:3100"
    volumes:
      - /srv/loki/config/loki-config.yml:/etc/loki/config.yml:ro
      - /srv/loki/data:/loki
    command: -config.file=/etc/loki/config.yml
    networks: [observability]

  promtail:
    image: grafana/promtail:3.0.0
    container_name: promtail
    restart: unless-stopped
    volumes:
      - /srv/promtail/config/promtail-config.yml:/etc/promtail/config.yml:ro
      - /var/log:/var/log:ro
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
    command: -config.file=/etc/promtail/config.yml
    depends_on: [loki]
    networks: [observability]

  uptime-kuma:
    image: louislam/uptime-kuma:1
    container_name: uptime-kuma
    restart: unless-stopped
    ports:
      - "3001:3001"
    volumes:
      - /srv/uptime-kuma/data:/app/data
    networks: [observability]

networks:
  observability:
    driver: bridge
```

### Initial Loki Failure
Loki failed to start due to:
- Volume permission errors
- Retention enabled without delete-request-store
- Incorrect path references (/srv vs /loki)

>mkdir /loki/rules: permission denied
invalid compactor config: delete-request-store not configured

Fixes Applied
- Corrected volume paths to match container expectations
- Created missing WAL directory
- Fixed ownership to Loki UID (10001)
- Simplified config to filesystem-backed storage
- Removed compactor retention

```bash
chown -R 10001:10001 /srv/loki
mkdir -p /srv/loki/wal
```

Updated Loki config to use /loki/* paths inside container.

Loki Validation

```bash
curl http://10.10.10.71:3100/ready
```

Initial response:
>Ingester not ready  
After ~3 minutes:
ready  

Loki is up but unresponsive.  

---

## NoctHaven Node Expansion

**New Services Added**  
- Audiobookshelf (Audiobooks & podcasts server)
- Monica (Personal CRM)
- Heimdall (Guest Dashboard )
- Mealie (Recipe manager)
- CUPS (network printing)
- Gitea (internal Git and writeups)

All services deployed via existing Docker Compose stack.

---

## Service Failures and Debugging
### CUPS 
- Container starts but no logs  
- Service is unreachable  
- Stopped for later investigation 
  
### Monica
1. **Unable to contact your database**  
**APP_KEY already set**

  - Actions taken:
    - Verified volume mounts
    - Corrected permissions (www-data UID 33)
    - Manually created sqlite database

2. **Multiple restarts**  

  - Result: Still unresponsive  
  - Decision: Stop container and research offline

### Gitea Deployment
Initial failure caused by malformed app.ini.

>key-value delimiter not found: Ini

Root cause: stray literal Ini at top of config file.  

Fix:
- Removed invalid config
- Let Gitea regenerate defaults
>Result: Gitea started successfully.

Current State  
Working

- Uptime Kuma
- Audiobookshelf
- Heimdall
- Mealie
- Gitea

Not Working

- Monica
- CUPS
>Both intentionally stopped pending research.  
- Loki
- Promtail?

### Homer Dashboard Updates
Added Uptime Kuma and newly added services to Homer.  

---

## Updated Hardware Inventory

| Device | CPU | RAM | Storage | Role | IP |
|---|:---:|:---:|:---:|---|---|
|Router (SOHO)|-|-|-|Default Gateway / DHCP|10.10.10.1|
|Switch 1 (TP-Link)|-|-|-|Smart Switch|10.10.10.2|
|Switch 2 (Netgear)|-|-|-|Smart Switch|10.10.10.3|
|Proxmox Node 1 - NoctHaven|i5-7500|24 GB|256 GB nvme + 1 TB SSD|Nextcloud, Vaultwarden, Jellyfin, Portainer, MariaDB|10.10.10.20|
|Proxmox Node 2 – NyxNode2|Ryzen 5 Pro 2400GE|16 GB|256 GB nvme + 225 GB SSD|AdGuard Home, Ansible, Grafana, Prometheus, Unbound, Watchtower, Traefik|10.10.10.60|
|Proxmox Node 3 - Nyxnode3|Ryzen 5 Pro 2400GE|16 GB|256 GB nvme + 1 TB SSD|Loki, Promtail, Uptime Kuma|10.10.10.70|
|Proxmox SOC – Black Aegis (Coming soon)|i5-6500|64 GB|256 GB HDD + 2 TB HDD|  Security Onion, Cortex, SEEK, The Hive|10.10.10.30|
|Proxmox Lab Node|i5-6500T|32 GB|256 GB SSD|DVWA, Juice Shop, WebGoat, WP Vuln, Kali Purple, Windows Server, Windows 11 Pro|10.10.10.40|
|Laptop “Nyx Core”|Ryzen 7 7739U|64 GB|500 GB SSD|Admin / Management|10.10.10.5|
|Printer HP Officejet Pro 6968|500 MHz|1 GB|-|Network scanner|DHCP|

>RAM Maxed on SOC and Lab Nodes 6
 GB. Node 1 RAM increased to 24 GB. Node 2 increased to 16 GB. Node 3 increased to 16 GB.

---

## Containers

### Total Running Containers

|Name|Image|Status|Node|
|---|---|---|---|
|Homer|b4bz/homer:latest|Up|1|
|Maria DB|mariadb:10.11|Up|1
|Nextcloud|nextcloud:apache|Up|1
|Portainer|portainer/portainer-ce:latest|Up|1
|Mealie|ghcr.io/mealie-recipes/mealie:latest|Up|1
|Audiobookshelf|ghcr.io/advplyr/audiobookshelf:latest|Up|1
|Heimdall|lscr.io/linuxserver/heimdall:latest|Up|1
|Gitea|gitea/gitea:latest|Up|1
|BookLore|booklore/booklore:latest|Up|1
|Jellyfin|Media server|Up,transcoding failure|1|
|AdGuard Home|adguard/adguardhome|Up, unconfigured|2
|Grafana|grafana/grafana:latest|Up|2
|Prometheus|prom/prometheus:latest|Up, unresponsive|2
|Unbound|mvance/unbound:latest|Up|2
|Watchtower|containrrr/watchtower:latest|Up|2
|AlertManager|prom/alertmanager:latest|Up|2
|Uptime Kuma|louislam/uptime-kuma:1|Up|3
|dvwa|vulnerables/web-dvwa|Up|Lab|
|juiceshop|bkimminich/juice-shop|Up|Lab|
|webgoat|webgoat/webgoat-8.0|Up|Lab|
|Kali Purple|iso|Up|Lab|

### Down/Pending Containers

|Name|Role|Status|
|---|---|---|
|CUPS|Scanner interface only|down, unconfigured|
|Monica|Personal CRM|down, unconfigured|
|Immich|Photo/video management|Post RAM transcoding fix|
|Loki|Centralized log aggregation|down, unconfigured|
|Promtail|Log collection|down, unconfigured|
|Syncthing|Continuous file synchronization|Coming Soon|
|Syncthing|Synchronization target|Coming Soon|
|Home Assistant|Automation platform|coming soon|
|Samba|File sharing for scanning/media|Coming soon|
|Proxmox Backup Server|Cluster backup coordination|Up directly before Ansible|
|Ansible|Automation controller|Up directly before Traefik|
|Traefik|Reverse proxy / SSL| To be added when network becomes public facing|
|Vaultwarden|Secure password vault|Inoperable until https (Traefik)|

---

## Lessons Learned

1. Quorum solves one problem and exposes others. Adding a third Proxmox node stabilized cluster state, but it immediately surfaced gaps in observability, logging, and configuration consistency.    

2. Permissions can fail both loudly and silently. Loki failed loudly. Monica failed silently. Both cost time, but silent failures are worse because they foster confusion.

3. Not all “simple” services are actually simple. Monica and CUPS looked trivial on paper and were less so in practice. Stateful apps demand more attention than stateless media or dashboard services.

4. Stopping a broken service is a valid engineering decision
Pausing Monica and CUPS was not giving up, it was preserving system stability. Knowing when to stop is as important as knowing how to fix.

5. Observability should be deployed early, not retrofitted. Logging and uptime checks should exist before service sprawl begins.

6. Time delays can masquerade as failures. Loki’s delayed readiness looked broken until it wasn’t. Not every error is fatal. Distinguishing between slow initialization and true failure prevents unnecessary rework.

7. Documentation of mistakes converts pain into leverage. Every failure documented here reduces future recovery time to minutes instead of hours. 

---

## Next Steps
1. Configure Monica (Priority).
2. Add Speedtest service to Node 2.
3. Verify Audiobookshelf directory contents: /config & /metadata.
4. Install Newly acquired Node (nyxnode1).
5. Migrate Services from Nocthaven to nyxnode1.
6. Configure Jellyfin transcoding.
7. Turn Nocthaven into a pfsense node and place soho router in bridge mode (plan modem and AP replacement.
8. Add VLAN seperation to network.
9. Add Portainer and Watchtower agents to additional hosts.
10. Configure containers to healthy status.
11. Document networking layout for cross host Docker management.

---

## Reference

- [Audiobookshelf](https://www.audiobookshelf.org/guides/)
- [Monica](https://docs.monicahq.com/)
- [Heimdall](https://heimdall.site/)
- [Mealie](https://docs.mealie.io/)
- [CUPS](https://openprinting.github.io/cups/)
- [Gitea](https://docs.gitea.com/)
- [Loki](https://grafana.com/docs/loki/latest/)
- [Promtail](https://grafana.com/docs/loki/latest/send-data/promtail/)
- [Uptime Kuma](https://github.com/louislam/uptime-kuma/wiki)

---

Environment: **Project Nyxline Home Lab**  
Report: **Cluster Expansion and Observability Deployment**  
