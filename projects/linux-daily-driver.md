# Daily Driver Build: Acer Aspire A315-58-59TK  
**Date:** 2025-04-28  
**System Role:** Daily Driver / Lab-Ready Linux Workhorse  
**Distribution:** Pop!_OS 22.04  

## Objective  
Transform a budget Acer laptop into a high-performance Linux machine for productivity, cybersecurity labs, content creation, and writing. Maintain a polished, stable daily driver environment without compromising on nerd cred or utility.

## Hardware Overview  
- **Model:** Acer Aspire A315-58-59TK  
- **Original Specs:**  
  - RAM: 8GB (4GB soldered + 4GB DIMM)  
  - Storage: 256GB SSD  
- **Current Specs:**  
  - RAM: 20GB (4GB soldered + 16GB DDR4 3200MHz)  
  - Storage: 1TB SSD  
- **Planned Upgrade:** 36GB RAM (Max DIMM: 32GB — unofficial but supported)

## Actions Taken  
- Swapped out the factory SSD with a 1TB unit for proper storage breathing room.  
- Upgraded to 20GB of RAM. Acer claims that’s the limit.  
- Confirmed unofficial RAM support for up to 36GB based on chipset documentation and international forum wisdom.  
- Installed **Pop!_OS 22.04** — chosen for its stability, hardware support, and clean GNOME-based interface.  
- Disabled Secure Boot (twice — BIOS sometimes plays peekaboo with settings).  
- Installed core software for productivity and creation:  
  - **LibreOffice** (Docs), **Manuskript** (Outlining), **Ghostwriter** (Markdown), **Geany/Vim** (Code)  
  - **Darktable** (Photos), **Flowblade** (Video), **Scribus** (Layout)  
- Added cybersecurity essentials: **Wireshark**, **OWASP ZAP**, **nmap**, and supporting toolkits.  
- Set up **Timeshift** for weekly snapshots, especially important since 22.04 is still maturing.  

## Outcomes  
- Pop!_OS runs smoothly and fast — a welcome surprise considering the base hardware.  
- No driver drama. Everything worked out of the box, minus Secure Boot hiccups.  
- Confirmed that system manufacturers regularly understate hardware potential. Always verify, especially if you’re not in the US market.  
- Laptop now pulls double duty as a polished daily machine and a capable cybersecurity lab.  

## Lessons & Observations  
- BIOS settings don’t always save the first time. Apply changes, reboot, then double-check.  
- Linux installation in 2025 is smoother than ever — unless you're dealing with Apple hardware (more on that below).  
- With this machine, I can write, test tools, build scripts, and break things — then write about breaking them.  
- I’m one 32GB DIMM away from 36GB RAM in a sub-$500 build. That’s not just efficient — it’s a middle finger to overpriced ultrabooks.  

## Final Notes  
Pop!_OS walks the line between refined and powerful better than most distros. While I don’t worship at the altar of System76, I respect their craftsmanship. Maybe one day I’ll snag one of their machines—or shoehorn Asahi Linux onto an old M2 MacBook just to prove a point.  

Until then, this Acer will do just fine. It doesn’t phone home, doesn’t require an Apple ID, and it never randomly decides it's “Updating your system… please wait.”
