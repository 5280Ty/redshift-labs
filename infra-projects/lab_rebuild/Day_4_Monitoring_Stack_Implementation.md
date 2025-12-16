# Day 4 – Network Monitoring, DNS, and Service Expansion

Author: Ty  
Date: November 2025  
Environment: Project Nyxline Home Lab  

---

## Project Overview

Day 4 focused on introducing centralized monitoring, alerting, and DNS infrastructure while continuing service expansion across the Proxmox cluster. This overview intentionally notes several failure that are documented here for auditability and future remediation.

### Primary goals:

1. Deploy a Prometheus, Grafana, and Alertmanager monitoring stack.
2. Establish baseline alerting for node health and resource exhaustion.
3. Introduce network level DNS filtering and recursive resolution using AdGuard Home and Unbound.
4. Expand content services with BookLore.
5. Update cluster inventory and service state tracking.

---

## Monitoring Stack – Design 

### Initial Concept (Discarded)

Early design relied on a minimal Prometheus and Grafana pair with Watchtower. This was abandoned once alerting, rule separation, and provisioning needs were better understood.
```yml
services:
  prometheus:
    image: prom/prometheus:latest
    ports:
      - "9090:9090"
  grafana:
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
```

This approach lacked alert routing, rule management, and reproducibility.


---

### Final Monitoring Architecture

docker-compose.yml

```yml
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    restart: unless-stopped
    ports:
      - "9090:9090"
    volumes:
      - /mnt/log-storage/prometheus:/prometheus
      - /mnt/configs/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - /mnt/configs/prometheus/rules:/etc/prometheus/rules

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    restart: unless-stopped
    ports:
      - "3000:3000"
    volumes:
      - /mnt/log-storage/grafana:/var/lib/grafana
      - /mnt/configs/grafana/provisioning:/etc/grafana/provisioning
      - /mnt/configs/grafana/dashboards:/var/lib/grafana/dashboards

  alertmanager:
    image: prom/alertmanager:latest
    container_name: alertmanager
    restart: unless-stopped
    ports:
      - "9093:9093"
    volumes:
      - /mnt/log-storage/alertmanager:/alertmanager
      - /mnt/configs/alertmanager/config.yml:/etc/alertmanager/config.yml

  watchtower:
    image: containrrr/watchtower:latest
    container_name: watchtower
    restart: unless-stopped
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: --cleanup --schedule "0 0 4 * * *"
```

---

## Directory Layout

### Prometheus
mkdir -p /mnt/log-storage/prometheus
mkdir -p /mnt/configs/prometheus/rules

### Grafana
mkdir -p /mnt/log-storage/grafana
mkdir -p /mnt/configs/grafana/dashboards
mkdir -p /mnt/configs/grafana/provisioning/{datasources,dashboards}

### Alertmanager
mkdir -p /mnt/log-storage/alertmanager
mkdir -p /mnt/configs/alertmanager

---

## Prometheus

### Prometheus Configuration
```
rule_files:
  - "/etc/prometheus/rules/node_alerts.yml"

alerting:
  alertmanagers:
    - static_configs:
        - targets: ["10.10.10.61:9093"]

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["10.10.10.61:9090"]

  - job_name: "node_exporter"
    static_configs:
      - targets:
          - "10.10.10.22:9100"
```

### Alert Rules
```yml
groups:
  - name: node.rules
    rules:
      - alert: InstanceDown
        expr: up == 0
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "Instance {{ $labels.instance }} down"

      - alert: HighCPU
        expr: 100 - (avg by(instance)(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 85
        for: 5m
        labels:
          severity: warning

      - alert: HighMemoryUsage
        expr: (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100 > 80
        for: 2m
        labels:
          severity: warning
```

---

## Grafana Provisioning

### Datasource
```yml
apiVersion: 1
datasources:
  - name: Prometheus
    type: prometheus
    access: proxy
    url: http://10.10.10.61:9090
    isDefault: true
```

### Dashboards

Dashboards are auto loaded from /var/lib/grafana/dashboards. Initial JSON focuses on CPU and memory.

---

## Node Exporter (All Nodes)
```yml
services:
  node_exporter:
    image: quay.io/prometheus/node-exporter:latest
    container_name: node_exporter
    restart: unless-stopped
    network_mode: host
    pid: host
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
```

---

## DNS Stack – New VM (Node 202)

### Purpose

Introduce network wide DNS filtering and independent recursive resolution.

### Stack
* AdGuard Home (filtering - frontend)  
* Unbound (recursive resolver - backend)  


### Key Failure

* Port 53 conflict with systemd.
* Resolution steps:
* systemctl stop systemd-resolved
* systemctl disable systemd-resolved
* AdGuard failed.

---

## BookLore Deployment

BookLore was added as a long term eBook management service.

### Services

* MariaDB backend
* BookLore application container

### Notable Items

* Directory confusion during initial navigation
* Duplicate compose fragments corrected
* Successful bulk ingest via SCP
>scp -r ~/Documents/Books/* root@10.10.10.22:/mnt/dockerdata/booklore/bookdrop/

---

## Current Issues

1. Prometheus UI reachable but non responsive.
2. AdGuard and Unbound are not operational.
3. Vaultwarden remains blocked pending Traefik.
4. Jellyfin transcoding failure persists.

---

## Updated Hardware Inventory

| Device | CPU | RAM | Storage | Role | IP |
|---|:---:|:---:|:---:|---|---|
|Router (SOHO)|-|-|-|Default Gateway / DHCP|10.10.10.1|
|Switch 1 (TP-Link)|-|-|-|Smart Switch|10.10.10.2|
|Switch 2 (Netgear)|-|-|-|Smart Switch|10.10.10.3|
|Proxmox Node 1 – NoctHaven|i5-7500|24 GB|256 GB nvme + 1 TB SSD|Nextcloud, Vaultwarden, Jellyfin, Portainer, MariaDB|10.10.10.20|
|Proxmox Node 2 – NyxNode2|Ryzen 5 Pro 2400GE|12 GB|256 GB nvme + 225 SSD|AdGuard Home, Ansible, Grafana, Prometheus, Unbound, Watchtower, Traefik|10.10.10.60|
|Proxmox SOC – Black Aegis (Coming soon)|i5-6500|64 GB|256 GB HDD + 2 TB HDD| Security Onion, Cortex, SEEK, The Hive|10.10.10.30|
|Proxmox Lab Node|i5-6500T|32 GB|256 GB SSD|DVWA, Juice Shop, WebGoat, WP Vuln, Kali Purple, Windows Server, Windows 11 Pro|10.10.10.40|
|Laptop “Nyx Core”|Ryzen 7 7739U|64 GB|500 GB SSD|Admin / Management|10.10.10.5|
|Printer HP Officejet Pro 6968|500 MHz|1 GB|-|Network scanner|DHCP|
>RAM Maxed on SOC and Lab Nodes. Node 1 RAM increased to 24 GB.

---

## Containers

### Total Running Containers

|Name|Image|Status|
|---|---|---|
|Homer|b4bz/homer:latest|Up, Healthy|
|Maria DB|mariadb:10.11|Up|
|Nextcloud|nextcloud:apache|Up|
|Portainer|portainer/portainer-ce:latest|Up|
|dvwa|vulnerables/web-dvwa|Up|
|juiceshop|bkimminich/juice-shop|Up|
|webgoat|webgoat/webgoat-8.0|Up|
|Kali Purple|iso|Up|
|AdGuard Home|adguard/adguardhome|Up|
|Grafana|grafana/grafana:latest|Up|
|Prometheus|prom/prometheus:latest|Up, unresponsive|
|Unbound|mvance/unbound:latest|Up|
|Watchtower|containrrr/watchtower:latest|Up|
|AlertManager|prom/alertmanager:latest|Up|
|BookLore|booklore/booklore:latest|Up|


### Down/Pending Containers

|Name|Role|Status|
|---|---|---|
|Immich|Photo/video management|Post RAM transcoding fix|
|Jellyfin|Media server|Down for "Maint." transcoding failure|
|Audiobookshelf|Audiobooks & podcasts server|Coming soon|
|Loki|Centralized log aggregation|Coming Soon|
|Promtail|Log collection|Coming Soon|
|Proxmox Backup Server|Cluster backup coordination|Coming Soon|
|Uptime Kuma|Service uptime monitoring|Coming Soon|
|Syncthing|Continuous file synchronization|Up post node 3 SSD install|
|Syncthing|Synchronization target|Up post node 3 SSD install|
|CUPS|Scanner interface only|Up with node 4|
|Home Assistant|Automation platform|Up with node 4|
|Mealie|Recipe manager|Up with node 4|
|Ansible|Automation controller|Up with node 4|
|Samba|File sharing for scanning/media|Up with node 4|
|Vaultwarden|Secure password vault|Inoperable until https (Traefik)|
|Traefik|Reverse proxy / SSL| To be added when network becomes public facing|

---

Lessons Learned

1. Monitoring stacks fail silently when miswired. Alerting must be validated aggressively.

2. DNS services compete with systemd by default. Plan port ownership early.

3. Broken services are still progress when documented correctly.

4. Configuration sprawl is the enemy. Centralized provisioning is non negotiable.

---

## Next Steps

* Restore Prometheus responsiveness.  
* Integrate AdGuard as primary DNS.  
* Add Audiobookshelf and Heimdall to node 1.
* Add PiHole to node 1 (backup DNS).
* Add newly aquired node 3 to the cluster (Loki, Promtail, Proxmox Backup Server, Uptime Kuma).  
* Add Portainer and Watchtower agents to additional hosts.
* Update Homer dashboard with new applications.
* Document networking layout for cross host Docker management.
* Configure containers to healthy status.
* Fix Vaultwarden availability and admin page access post Traefik.

---

## Reference

[AdGuard Home docs](https://github.com/AdguardTeam/AdguardHome)  
[Grafana docs](https://grafana.com/docs/)  
[Prometheus docs](https://prometheus.io/docs/introduction/overview/)  
[Unbound docs](https://github.com/NLnetLabs/unbound)  
[Watchtower docs](https://containrrr.dev/watchtower/)  
[AlertManager docs](https://prometheus.io/docs/alerting/latest/alertmanager/)  
[BookLore docs](https://github.com/booklore-app/booklore)  
[Kali Purple](https://www.kali.org/docs/)  
[Windows Pro 11](https://learn.microsoft.com/en-us/windows/)  
[Windows Server](https://learn.microsoft.com/en-us/windows-server/)  

---

Environment: **Project Nyxline Home Lab**  
Report: **Monitoring and Alerting Implementation+**  
