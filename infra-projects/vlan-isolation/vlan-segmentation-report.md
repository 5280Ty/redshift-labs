## Date: 2024-11-07  
**Project:** VLAN Segmentation - Printer Isolation

---

### Objective  
Quarantine a vulnerable legacy printer on its own VLAN to eliminate lateral movement risk and shut down casual snooping from other endpoints.

---

### Tools  
- VLAN-capable managed switch  
- Basic switch CLI access  
- Ping, ARP scan, and manual recon tools  

---

### Method  
- Created **VLAN 10** for the printer, **VLAN 20** for office devices  
- Assigned ports to VLANs manually via switch CLI  
- Confirmed isolation using ICMP tests and ARP discovery attempts  
- Monitored traffic and verified no bleed between VLANs

---

### Outcome  
- Printer fully isolated, no cross-VLAN comms  
- Office VLAN unable to detect printer via standard discovery tools  
- ARP and route lookups confirmed logical separation

---

### Notes & Next Steps  
- ACLs will be added to harden inter-VLAN boundaries  
- VLAN configs will be scripted for repeatability  
- Additional segmentation planned (IoT, test bench, Flipper targets)
