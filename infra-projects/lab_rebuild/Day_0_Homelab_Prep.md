# Day 0 - Homelab Preparation

## Purpose

This homelab is basically my sandbox, a place to break, fix, and refine real IT and security skills without burning production gear. The lab is structured to replicate an enterprise style infrastructure, using multiple Proxmox hosts dedicated to production, testing, and security monitoring.

### Key Objectives
- Strengthen networking fundamentals  
- Master virtualization and self-hosting  
- Advance system administration and security monitoring  
- Build a realistic platform for continuous learning in IT and cybersecurity  

One Proxmox node hosts general services such as Nextcloud, Vaultwarden, and media management, while another runs Security Onion for network monitoring, intrusion detection, and log analysis.

This setup will keep me learning nonstop, from networking and system tuning, to network defense.

---

## Initial Hardware Inventory

### Servers

#### HP ProDesk 600 G2 SFF
- **CPU:** Intel i5-6500  
- **RAM:** 40 GB DDR4 (upgraded)  
- **Storage:** 256 GB SSD *(HDD will be added)*  
- **Role:** SOC Box (Security Onion / monitoring stack)  
- **NICs:** Dual-port + spare single NIC  

#### Dell OptiPlex 5050 SFF
- **CPU:** Intel i5-7500  
- **RAM:** 16 GB DDR4 (good enough but will be upgraded)  
- **Storage:** 256 GB SSD *(2nd SSD planned)*  
- **Role:** App Box (Nextcloud, Jellyfin, Vaultwarden, Portainer, MariaDB)  

#### Raspberry Pi 4
- **CPU:** Cortex-A72  
- **RAM:** 4 GB  
- **Role:** Pi-hole + Unbound  

#### HP Printer (T0F28A)
- **NIC:** Enabled (vulnerable if exposed)  
- **Role:** VLAN-isolated printing only  

#### Laptop “Nyx Core” (Admin)
- **CPU:** AMD Ryzen 7 7739U  
- **RAM:** 64 GB  
- **Storage:** 500 GB SSD  
- **Role:** Management device (not part of the lab network)

---

## Networking and Infrastructure

### Switches
- **Smart Switch 1:** TP-Link TL-SG108E  
- **Smart Switch 2:** Netgear  

### Router
- **Default Gateway:** `10.10.10.1`  
- Provides internet uplink and DHCP  
- Manual DNS: `9.9.9.9` and `1.1.1.1`  

### Misc
- **Server Rack:** GeekPi 10 inch 8U  
- **Television:** LG 65UP8000 (for monitoring)

---

## Setup and Configuration

1. Verified Windows license types using:
   ```bash
   slmgr /dlv
   ```
Documented one retail license for Windows 10 Pro.  


2. Extracted license with Show Key Plus.
Removed existing keys for clean builds:
```bash
slmgr /upk
slmgr /cpky
```
Not required, but I don't want issues down the line.  


3. Installed Proxmox VE on both servers.
Assigned FQDN (NoctHaven.XXX) (BlackAegis.XXX).


4. Readdressed network:

Old: 192.168.0.1  
New: 10.10.10.1  
DHCP Range: 10.10.10.100 - 10.10.10.200  


5. Assigned switch IPs:

TP-Link: 10.10.10.2  
Netgear: 10.10.10.3  
Upgraded firmware: TP-Link v6, Netgear v1.0.1.9  


6. Raspberry Pi failed diagnostics (8 flashes = RAM or power issue).
Raspberry Pi likely dead, Pi-hole will be virtualized. Unsure if this device is repairable.


7. Added 2TB HDD and dual NIC to SOC Box. Confirmed drive using:

```bash
lsblk
```

Confirming 2TB actually mounted.


8. Determined ISP router lacks VLAN support and has forced app configuration to use all features.
Replacement planned with OPNsense.


9. Identified printer vulnerabilities:

CVE-2017-2741  
CVE-2023-4694  
Device will remain isolated until hardened and/or router replacement.  




---

## Updated Hardware Inventory

| Device | CPU | RAM | Storage | Role | IP |
|---|:---:|:---:|:---:|---|---|
|Router (SOHO)|-|-|-|Default Gateway / DHCP|10.10.10.1|
|Switch 1 (TP-Link)|-|-|-|Smart Switch|10.10.10.2|
|Switch 2 (Netgear)|-|-|-|Smart Switch|10.10.10.3|
|Proxmox Node 1 – App Box|i5-7500|16 GB|256 GB SSD|Nextcloud, Vaultwarden, Jellyfin, Portainer, MariaDB|10.10.10.20|
|Proxmox SOC – Black Aegis|i5-6500|40 GB|256 GB HDD + 2 TB HDD| Security Onion / Monitoring|10.10.10.30|
|Proxmox Lab Node (Incoming)|i5-6500T|16 GB|256 GB SSD|DVWA, Juice Shop, WebGoat Containers|10.10.10.40|
|Laptop “Nyx Core”|Ryzen 7 7739U|64 GB|500 GB SSD|Admin / Management|10.10.10.5|



---

## Planned Virtual Machines and Containers

**Proxmox Node 1 (NoctHaven)**

VM1: Nextcloud, MariaDB, Jellyfin, Vaultwarden, Portainer, Homer

VM2: Pi-hole + Unbound

**SOC Node (BlackAegis)**

VM1: Security Onion (Full Stack)

**Lab Node (Redshift)** ***Coming Soon***

LXC Containers: DVWA, OWASP Juice Shop, WebGoat



---

## Next Steps

Assemble physical network in rack

Deploy services on Proxmox Node 1:

Nextcloud, Vaultwarden, MariaDB, Jellyfin, Portainer, Homer  


Install Security Onion on SOC Node

Upgrade RAM and prepare incoming Lab Node

Replace SOHO router with OPNsense (must do)

Test VLAN segmentation when the new router is installed

Spin up Pi-hole + Unbound as VM



---

## Notes

Raspberry Pi 4 confirmed nonfunctional (8 flash boot failure)

ISP router rejected due to vendor lock, forced app setup, no thanks.

HP OfficeJet Pro 6968 remains offline pending hardening or VLAN isolation



---
Environment: Project Nyxline Homelab 
Project: Network Architecture & Deployment  
 
 
