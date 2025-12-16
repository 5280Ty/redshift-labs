# Day 3 - Securing & Optimizing Network Infrastructure: EWS Hardening + LAG Implementation   
**Author:** Ty  
**Date:** November 2025  

---

## Project Overview
This project focuses on hardening a networked printer and optimizing inter-switch connectivity through Link Aggregation Group (LAG) implementation. The objectives were to:

1. Minimize exposure of embedded web services (EWS) on the printer.  
2. Apply firmware updates to mitigate known critical vulnerabilities.  
3. Enforce secure network protocols and administrative access.  
4. Enhance inter-switch bandwidth for high-traffic lab and media workloads.  

The work aligns with [NIST Special Publication 800-53](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r5.pdf) controls: [PE-3 (Physical Access Control)](https://csf.tools/reference/nist-sp-800-53/r5/pe/pe-3/) and [AC-4 (Information Flow Enforcement) ](https://csf.tools/reference/nist-sp-800-53/r5/ac/ac-4/).

---

## Environment & Network Placement
- Printer: HP Officejet Pro 6968  
- Initial network IP: 10.10.10.xxx  
- Network context: connected to a stack of 1 Gb switches  
- Services running on network that require high throughput: Security Onion, Jellyfin, Syncthing, Proxmox Backup Server, Immich, Samba, MariaDB, and a future NAS node.  

The printer GUI was accessed without credentials via the local IP. Initial configuration review included disabling non-essential services and confirming IP assignment.

---

## Firmware Hardening

### Firmware Update
Firmware was updated from MCP2CN2415AR (2024-04-12) to MCP2CN2506AR (2025-02-07) via HP Printer Update.  

Addressed CVEs:
- [CVE-2017-12652:](https://nvd.nist.gov/vuln/detail/CVE-2017-12652) Arbitrary Code Execution
- [CVE-2020-14152:](https://nvd.nist.gov/vuln/detail/CVE-2020-14152) Denial of Service  
- [CVE-2022-2068:](https://nvd.nist.gov/vuln/detail/cve-2022-2068) Arbitrary Code Execution  
- [CVE-2023-45853:](https://nvd.nist.gov/vuln/detail/cve-2023-45853) Information Disclosure  

> Note: These vulnerabilities originate from third-party components in the printer driver and were labeled critical/high risk.

---

## EWS & Security Hardening

1. Disabled ePrint and Printables services.  
2. Disabled AirPrint/IPPS/IPP to prevent unauthorized mobile access.  
3. Redirected HTTP to HTTPS for secure GUI access.  
4. Disabled Bonjour/mDNS since Apple devices are not used.  
5. Disabled anonymous telemetry: turned off Usage Information Storage and Automatic transmission to HP.  
6. Set administrative username and password for EWS login.  

---

### Network Configuration
- Attempted IPv4 static IP: `10.10.10.70`  
- Gateway: `10.10.10.1`  
- DNS servers:
  - 9.9.9.9 (primary)  
  - 1.1.1.1` (secondary)  

>Static IP assignment failed via printer GUI due to router restrictions; continued to use DHCP reservation on the router for consistent addressing.  
- Disabled IPv6 to reduce attack surface.  
- Wireless access fully disabled.  

---

## LAG Implementation for Switches

### Switch Configuration
- Switches: 1 Gb stack, IPs `10.10.10.2` and `10.10.10.3`  
- LAG ports configured: ports 5 & 6 on both switches  
- Mode: standard load-balancing LAG

### Rationale
Multiple high-bandwidth services converge on this network. A single 1 Gb link could saturate during concurrent flows. By adding a second LAG line:

- Aggregate bandwidth increases to ~2 Gb.  
- Redundancy ensures continued connectivity if one link fails.  
- Load balancing allows simultaneous flows from Security Onion, Jellyfin, Syncthing, Proxmox Backup Server, Immich, Samba, and MariaDB to coexist without congestion.  

---

## Service vs Switch Load

| Service | Expected Switch Activity | Notes |
|---------|------------------------|-------|
| Security Onion | High | SOC data flows, IDS/IPS, log aggregation |
| Syncthing | High | Continuous file synchronization across nodes |
| Proxmox Backup Server | High | Backup operations to NAS |
| Jellyfin | High | Streaming media server traffic |
| MariaDB | High | Database queries for multiple services |
| Immich | High | Photo/video management, media uploads |
| Samba | Medium | File sharing for scans/media |
| Nextcloud | Medium | Cloud sync, personal cloud services |
| Vaultwarden | Medium | Password vault operations, low-volume traffic |
| Grafana | Medium | Metrics visualization, moderate traffic |
| Prometheus | Medium | Metrics collection, storage |
| DVWA | Low | Training web application |
| OWASP Juice Shop | Low | Vulnerable training web app |
| WebGoat | Low | Training web application |

>QuickSync will reduce the load produced by Jellyfin and Immich

---

## Lessons Learned
1. Consumer-grade printer firmware can be fragile; changes like static IP assignment may fail depending on router limitations. DHCP reservations are a reliable alternative.  
2. Firmware updates remain critical to mitigate CVEs from third-party components.  
3. EWS security relies on proper configuration of HTTPS, authentication, and disabling unnecessary protocols.  
4. Link Aggregation is critical in multi-service home labs to prevent inter-switch congestion.  
5. Network hardening should consider both protocol level security and physical access controls.  
6. Real-world troubleshooting is essential; documenting failed configurations helps future audits and reduces repeated mistakes.  

---

## Next Steps
1. Integrate the printer into VLAN-segmented lab network for additional isolation when available.  
2. Monitor printer and switch traffic for unusual patterns or bottlenecks.  
3. Extend LAG or migrate to 10 Gb infrastructure when additional high-bandwidth services are deployed.  
4. Document ongoing printer and switch maintenance in lab SOP for future audits.  

---

## Updated Hardware Inventory

| Device | CPU | RAM | Storage | Role | IP |
|---|:---:|:---:|:---:|---|---|
|Router (SOHO)|-|-|-|Default Gateway / DHCP|10.10.10.1|
|Switch 1 (TP-Link)|-|-|-|Smart Switch|10.10.10.2|
|Switch 2 (Netgear)|-|-|-|Smart Switch|10.10.10.3|
|Proxmox Node 1 – NoctHaven|i5-7500|16 GB|256 GB SSD + 1 TB SSD|Nextcloud, Vaultwarden, Jellyfin, Portainer, MariaDB|10.10.10.20|
|Proxmox SOC – Black Aegis (Coming soon)|i5-6500|40 GB|256 GB HDD + 2 TB HDD| Security Onion / Monitoring|10.10.10.30|
|Proxmox Lab Node|i5-6500T|16 GB|256 GB SSD|DVWA, Juice Shop, WebGoat Containers|10.10.10.40|
|Laptop “Nyx Core”|Ryzen 7 7739U|64 GB|500 GB SSD|Admin / Management|10.10.10.5|
|Printer HP Officejet Pro 6968|500 MHz|1 GB|-|Network scanner|DHCP|

---

Environment: **Project Nyxline Home Lab**  
Report: **Printer Hardening & LAG Implementation**  
