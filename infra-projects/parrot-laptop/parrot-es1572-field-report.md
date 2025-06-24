# Parrot OS Rebuild: Acer ES1-572-321G

**Redshift Protocol Internal Asset ID:** parrot-es1572  
**Date Deployed:** 2024-09-27  
**Distro Version:** Parrot OS 6.1 “Lorikeet”  
**Use Case:** Lab-only offensive security platform  
**User Data Present:** None (NO PII)

---

## Hardware Specs

| Component       | Description                     |
|----------------|---------------------------------|
| Laptop Model    | Acer Aspire ES1-572-321G         |
| CPU             | Intel Core i3-7100U (2.4GHz)     |
| RAM             | 16GB DDR3L (upgraded to "max")   |
| Storage         | 256GB SSD (SATA, clean install)  |
| WiFi            | Built-in card                    |
| Peripherals     | USB mouse                        |

---

## Build Process

### 1. **Prep and Hardware Modifications**
- Swapped in a blank SSD.
- Maxed out RAM (according to acer) to 16GB (removed 1x4GB original, added 2x8GB).
- No BIOS changes required.
- Boot mode: UEFI (default settings).

### 2. **OS Installation**
- Parrot OS 6.1 “Lorikeet” Home Edition ISO written to USB.
- Verified SHA256 sum before flashing.
- Clean install with full disk encryption.
- Chose not to dual boot; system is fully dedicated to lab ops.

### 3. **Initial Configuration**

- Sudo required for root tasks - preferred for command discipline.
- Confirmed apt sources were functional when tested with Ethernet.

---

## Use Case Role

This box exists purely for offensive lab training - TryHackMe, CTF prep, and experimentation with tools like:
- Burp Suite
- John the Ripper
- Nmap
- Wireshark
- Metasploit
- Gobuster & custom fuzzers

It is *not* used for productivity, personal data, or general browsing. Risk is intentionally compartmentalized.

---

## Future Plans
- Evaluate hopping to Pentoo for a more hardened, minimal build.
- Explore adding Kali Purple apps for maximum flexability of the machine.
- Explore adding more RAM for cracking passwords and hashes. Support found for both 32GB and 64GB.
- Stage toolchains using apt, snap, or manual builds as needed.
- Potential PXE boot project from core Redshift hub.

---

## Summary

This Parrot OS build is a "sterile", purpose-built offensive lab platform. It's fast, minimal, and disposable by design. Root access is constrained via sudo (as opposed to Kali). All modifications aim to maximize training value while minimizing attack surface. Redshift Protocol asset logged and deployed.
