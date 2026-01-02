# Day 5 - Canary Services Deployment
**Author:** Ty  
**Date:** November 2025

---

## Objective

Today focused on adding canary services into the network as early warning traps. These function like digital tripwires that alert and create a log when someone touches what they shouldn’t. They can sit quietly in directories, network shares, or services.

This document covers how I evaluated each node, what belonges where, and what may not.

The reason I decided to add Canary Tokens to my network is they can provide information about the attacker’s methodologies. This can be a great aid in mapping the attackers chosen attack surface.

---

## Thought Process
>The whole point of a canary is to get hit early in the kill chain. If its hidden in a dead corner of the network, it may never be tripped. If you put it next to the crown jewels, you risk creating noise where stability actually matters.

>Honeypots and decoys placed **only** next to the most important assets is a mistake. Attackers may look there last, not first.

>Sticking deceptive devices on a SOC system could create multiple issues. Also with the current SOC stack its not a believable decoy. 

> A Canary token exploit would be devistating inside the SOC node.

>The big decision was whether to place a canary on a node that already handles infrastructure traffic. If so, would it increase risk or visibility.

>Traffic choke points are the "Sweet Spot". Anything scanning the network or snooping on internal services is going to encounter Node 2 sooner or later. If something hits it, I will be notified.

---

### Reasons not to put Canary Tokens on each node

|Node|Reason|Notes|
|---|---|---|
|Node 1|Too important and too busy|	Stability beats curiosity|
|Node 2|Adds minor attack surface|But the impact is small compared to its strategic advantage|
|Node 3|File confusion|Mixing real logs with deception artifacts invites confusion|
|Node 4|Very Low traffic|Decoys here are more likely to be ignored|
|SOC Node|Dedicated to analysis|Could create confusion in a crisis|
|Lab Node|Its Intentionally vulnerable|A honeypot inside a cyber range?|

---

### Reasons to put Canary Tokens on each node

|Node|Reason|Notes|
|---|---|---|
|Node 1|Its the Primary node|Canary alerts vital if compromised|
|Node 2|Heaviest traffic|Optimal choke point
|Node 3|Backups are high-value targets|Ransomeware Gold Mine|
|Node 4|Very Low traffic|One hit on this node is a "Red Alert"
|SOC Node|R&D|All alerts should be sent to this node|
|Lab Node|Additional decoy endpoints|Lateral movement detection|

---

## Conclusion

I won’t be publishing the final placement choices. The point of defense in depth is that attackers shouldn’t know where the tripwires are or how many they’ll cross before alarm bells start ringing.

Canary tokens are the first layer. More layers are coming, they will not be documented here.

---

## References

https://help.canary.tools/hc/en-gb/articles/10905485310109-Canarytoken-Overview-and-Use-Cases

https://www.fortinet.com/resources/cyberglossary/what-is-canary-in-cybersecurity

https://fidelissecurity.com/cybersecurity-101/deception/canary-tokens/

---


Environment: **Project Nyxline Home Lab**  
Report: **Canary Token Deployment Considerations** 
