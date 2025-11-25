
<p align="center">
<img src="https://github.com/5280Ty/5280Ty/blob/main/Parrot.png" alt="BlackThorn" width="350"/>
</p>

<h1 align="center">Parrot OS Rebuild: Acer ES1-572-321G</h1>

This project documents the conversion of an older laptop into a dedicated offensive security and penetration testing platform using Parrot OS. The goal was to create a lightweight, mobile lab environment for field testing, red team practice, and hands-on tool development, without the overhead of a full desktop setup.

## Objectives

- Repurpose existing hardware to maximize utility and reduce e-waste
- Install and configure Parrot OS (Security Edition) for pentesting workflows
- Harden the system for both offensive and defensive operations
- Integrate with a home lab (VLAN separated, firewalled, and logged)
- Ensure portability for on-site assessments and wireless audits

## Hardware

- **Model:** ES1-572-321G 
- **RAM:** 16 GB
- **Storage:** SSD (upgraded)
- **Network:** Dual band WiFi, Ethernet via USB-C dongle (ethernet port does not work properly)

## Software Stack

- **OS:** Parrot OS (Security Edition)
- **Key Tools:** Burp Suite, Metasploit, Wireshark, ffuf, John the Ripper, Aircrack-ng, Ghidra

## Use Case

This build serves as a hybrid training and engagement machine. It supports local VM analysis, wireless reconnaissance, web app testing (including OWASP Juice Shop), and offensive recon scripting. It also ties into a segmented home lab environment for safe multi-target testing.

## Notes

- Tuned for battery conservation without sacrificing capability
- All telemetry disabled; no third-party analytics or "phone home" behavior
- Local logs forwarded to central syslog on NAS for correlation

## Future Enhancements

- Install RedELK for activity logging and visibility
- Integrate with Flipper Zero over USB and GPIO
- Build a persistent USB-based backup image for field recovery

---
