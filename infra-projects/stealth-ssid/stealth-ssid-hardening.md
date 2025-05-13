## Date: 2025-03-15

**Project:** SSID Stealth Attempt via Firewall Rules

**Tools Used:**
- SOHO Router Interface
- Firewall/ACL Configuration

**Objective:**
Reduce wireless visibility to mitigate opportunistic attacks and casual wireless enumeration.

**Actions Taken:**
- Accessed firewall configuration and disabled SSID broadcast manually.
- Applied changes, confirmed SSID hidden from standard scans (e.g., via phone).
- After a power cycle, SSID broadcast reverted to default enabled.

**Findings / Results:**
- Broadcast suppression works, but settings don’t persist across reboots.
- Router likely lacks non-volatile storage for advanced configs.

**Lessons Learned / Next Steps:**
- Consider moving SSID config to CLI (if supported) or switching to OpenWRT-capable device.
- Hidden SSIDs are a weak defense, but good(ish) layered opsec.
- Will pair with MAC filtering and EAP if feasible later.
