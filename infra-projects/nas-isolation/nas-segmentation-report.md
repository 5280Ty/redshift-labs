## Date: 2024-02-20

**Project:** NAS Segmentation & WiFi Band Optimization

**Tools Used:**
- Old Dell OptiPlex PC (converted to NAS)
- VLAN Management Tools (Switch CLI)
- SOHO Router Configuration Interface

**Objective:**
Segment DIY NAS from primary office devices and optimize WiFi performance.

**Actions Taken:**
- Installed lightweight Linux distro on OptiPlex and configured static IP.
- Placed NAS into VLAN 30, separate from office VLAN.
- Reconfigured SOHO router to split devices:
- 2.4 GHz for IoT and home devices.
- 5 GHz for laptops, tablets, and mobile devices.
- Disabled SSID broadcast through firewall settings.

**Findings / Results:**
- NAS traffic isolated from daily workstation traffic.
- Improved WiFi speed and stability for primary devices.
- SSID stealth setting reverted after power cycle.

**Lessons Learned / Next Steps:**
- Need a more robust router/firewall solution that retains custom settings after reboot.
- Evaluate using OpenWRT or pfSense for greater control.
- Plan to enforce additional WiFi client separation measures.
