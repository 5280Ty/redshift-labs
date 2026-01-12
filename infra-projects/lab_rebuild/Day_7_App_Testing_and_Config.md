
# Day 7 - App Testing and Configuration Report
Author: Ty  
Date: January 2026  
Environment: Project Nyxline Home Lab  

---

## Objective
This phase focused on expanding core lab services while deliberately stress-testing Docker, storage permissions, networking boundaries, and service isolation decisions. The goal was not just to “get things running,” but to understand where design choices could introduce attack paths or instability.

Several services deployed cleanly. Others fought back hard. Those "fights" turned out to be the most valuable part of the experience.

---

## Portainer Agent Deployment and Threat Modeling
Portainer Agent was deployed only on client nodes, not on the lab node. This was a deliberate security decision rather than a convenience tradeoff.  
A full attack chain was identified and treated as credible:  
>DVWA > container escape > Docker socket access > Portainer Agent > full cluster control  

Because the lab node hosts intentionally vulnerable workloads, exposing Portainer there would collapse segmentation and invalidate later adversarial testing. This decision reinforces a core principle: management planes must never coexist with deliberately compromised surfaces.

Result: Portainer Agents run only where blast radius is acceptable.  

---

## Monica CRM - Failure Analysis and Root Cause
Monica was initially deployed using SQLite to avoid standing up yet another database. On paper, this should have worked. In practice, it failed repeatedly with exit code 137 and a loop “waiting for database to settle” state.

### Key observations:  
1. Memory was not the issue. Over 11 GB RAM was free.  
2. File ownership and permissions were correct and verified from inside the container.  
3. SQLite file existed, was writable, and could be accessed by PHP.  
4. PHP SQLite modules were present.  

Despite all of this, Monica never progressed past initialization.
At this point, my assumption that “SQLite is simpler” was invalidated. Research confirmed that Monica behaves far more reliably with MariaDB and is known to hang silently on SQLite in containerized environments.  

### Decision: 
Monica was removed entirely and will be added to a clean VM with a dedicated database stack. This will avoid contaminating the current node with half-functional state and reinforced a rule going forward, no app gets special treatment just because it is “supposed to be lightweight.”

---

## CUPS - Container Reality vs Documentation
CUPS was one of the most time-consuming services to stabilize (yes, really).
Initial failures were silent. No logs. No listening port. No kernel messages. Just a container that refused to stay alive.  

### Key findings:
1. Several CUPS images fail hard if configuration directories are empty.  
2. Permissions alone were not the issue.  
3. AppArmor and seccomp restrictions in VM environments caused unpredictable behavior.  
4. Some images assume bare-metal USB access that may not translate cleanly into Proxmox VMs.  

After cycling through multiple images, debug entrypoints, security profile "relaxations", and volume alterations, stability was achieved using an alternate image with explicit admin credentials.  

### Lesson learned: 
Printer services are deceptively hostile to containerization. Printer is also a legacy machine running legacy software. This requires isolation, and validation.  

---

## Pi-hole - DNS Architecture Correction
The original design placed Pi-hole and Unbound together in a VM. This was later identified as a structural mistake (This decision was the real mistake).

### Corrective actions:
1. VM was destroyed.  
2. Pi-hole was rebuilt as a container.  
3. Unbound was decoupled and planned as a separate service.  
4. DNS responsibilities will be split to avoid a single point of failure.  

This redesign better reflects real enterprise DNS patterns: layered resolvers, redundancy, and role separation.

Pi-hole deployed cleanly, registered in Portainer, and added to Homer.  
Unbound will be added to the node to avoid another single point of failure.

---

## AdGuard Home + Unbound - Port Conflicts and Compose Drift
AdGuard Home and Unbound were deployed with static internal IPs on a custom Docker bridge network. The intent was: AdGuard as the filtering frontend, Unbound as the recursive resolver.  
Reality intervened.  

### Observed issues:
1. Port 53 conflicts despite containers reporting healthy states.
2. Docker proxy bindings behaved inconsistently across restarts.
3. docker-compose v1.29.2 produced different runtime behavior than the Compose plugin.
4. Services alternated between reachable and unreachable with identical configs.

Root cause was not a single misconfiguration, but toolchain inconsistency combined with an unfinished base VM. The system had remnants of mixed Ubuntu and Debian assumptions, outdated Compose tooling, and broken repository state.

### Resolution:
1. Repository cleanup
2. Docker Compose plugin installation
3. GPG key repair
4. Temporary DNS override to regain package access

After standardizing the environment, AdGuard stabilized. This reinforced a non-negotiable rule going forward: base OS hygiene matters more than YAML polish.

---

## Home Assistant - A Rare Win
Home Assistant deployed cleanly using host networking. No drama. No mystery restarts. Immediate UI access.

No notes, I'll take the W.

---

## Observability Stack - Prometheus, Loki, Promtail Update
Prometheus was restored by correcting bind-mounted configuration paths. 
Loki was rebuilt from scratch with filesystem storage and explicit schema configuration.
Promtail was identified as a mandatory per-node agent, not a centralized service. 
This reinforces proper log provenance and aligns with realistic SOC data flows.

The monitoring stack is now modular, explicit, and repeatable.

---

## Immich - Resource and Security Check
Immich deployed successfully with Postgres, Redis, and machine learning components. Resource usage was observed and documented.
Despite its complexity, Immich behaved predictably and stayed within reasonable memory bounds.

The Immich deployment also triggered a broader Homer security improvement by adding target="_blank" and understanding noopener noreferrer implications. Small UI decisions can have security consequences.

---

## Vaultwarden - Controlled Rollback
Vaultwarden deployment was intentionally downgraded due to unresolved environment variable handling and secrets exposure concerns. Rather than forcing a setup, the service was paused pending .env hardening and Traefik installation as Vaultwarden is https dependent.

This reflects a personal shift in mindset: not every service needs to be live immediately. Stability and intent matter more than checklists.

---

## Homer - Dashboard and Security
Homer evolved from a convenience dashboard into a curated control plane:
1. External logos replaced font icons for clarity, consistency, and aesthetics.
2. Dangerous services were explicitly labeled.
3. Target blank isolation was added globally.
4. Messaging warns against unsafe access to cyber range. The dashboard now communicates risk, not just availability.

---

## Final Observations
1. This phase reinforced several hard truths:
2. Tooling drift creates ghosts that waste hours.
3. “Lightweight” apps can be more fragile than heavy ones.
4. DNS is never simple.
5. Removing a service is sometimes the most practical outcome.
6. The lab is now more resilient, more intentional, and more honest about its own edges. 
7. Failures were documented (As always). 

Raw notes to be sanitized and add to repo.

---


## Docker Runtime Overview

### Docker Stats
**Nocthaven** 
docker stats --no-stream

|CONTAINER ID|NAME|CPU %|MEM USAGE / LIMIT|MEM %|NET I/O|BLOCK I/O|PIDS|
|---|---|---|---|---|---|---|---|
|c61eec625934|immich-server|0.04%|352.9MiB / 17.56GiB|1.96%|29.9MB / 122MB|2.18MB / 0B|24|
|a3b010416eec|immich-postgres|0.00%|23.71MiB / 17.56GiB|0.13%|157kB / 411kB|0B / 123MB|23|
|df83733e8371|immich-ml|0.13%|226.2MiB / 17.56GiB|.26%|34.9kB / 126B|0B / 24.6kB|9|
|f11000d32a71|booklore|0.20%|78.2MiB / 17.56GiB|3.21%|1.12MB / 436kB|8.19kB / 270kB|61|
|4161bbcde985|nextcloud|0.00%|109.9MiB / 17.56GiB|0.61%|263kB / 77.3kB|0B / 111kB|10|
|d6a4a612d9ab|mealie|0.11%|279.1MiB / 17.56GiB|1.55%|36.6kB / 126B|0B / 5.23MB   | 8|
|66791c437e0c|booklore-db|0.01%|84.18MiB / 17.56GiB|0.47%|472kB / 1.09MB|22.1MB / 35.8kB|11|
|bc71b9d08893|vaultwarden|0.00%|11.14MiB / 17.56GiB|0.06%|55.8kB / 6.36MB|12.9MB / 32.8kB|11|
|cc5be395380c|audiobookshelf|0.01%|35.76MiB / 17.56GiB|0.20%|36.1kB / 126B|0B / 41kB|12|
|4f73a387ba27|immich-redis|0.27%|4.426MiB / 17.56GiB|0.02%|128MB / 32.5MB|0B / 1.02MB|6|
|9902d1acecae|cups|0.00%|1.844MiB / 17.56GiB|0.01%|36.3kB / 126B|0B / 28.7kB     | 1|
|5fc1e055153c|heimdall|0.00%|130.7MiB / 17.56GiB|0.73%|50kB / 43.2kB|295kB / 171MB|20|
|ee7e0126041f|promtail|0.32%|31.26MiB / 17.56GiB|0.17%|36.4kB / 126B|270kB / 30.7MB|9|
|af93d04d7d6d|homer|0.00%|984KiB / 17.56GiB|0.01%|1.01MB / 14MB|2.77MB / 0B       |1|
|9b85f97cd4f4|nextcloud-db|0.01%|94.82MiB / 17.56GiB|0.53%|67.8kB / 215kB|49.4MB / 32.8kB|9|
|f68a13e85241|portainer|0.00%|16.95MiB / 17.56GiB|0.09%|25.5MB / 5.27MB|459kB / 100MB|7|
|e8202d6e90c1|gitea|0.00%|63.01MiB / 17.56GiB|0.35%|36.8kB / 126B|0B / 228MB       |13|
|b44051d1bcae|jellyfin|0.01%|139.3MiB / 17.56GiB|0.77%|210kB / 7.31kB|7MB / 67.6MB|17|


### Containers

#### Todocker statstal Running Containers

|Name|Image|Status|Node|
|---|---|---|---|
|Homer|b4bz/homer:latest|Up|1|
|Maria DB|mariadb:10.11|Up|1
|Nextcloud|nextcloud:apache|Up|1
|Portainer|portainer/portainer-ce:latest|Up|1
|Mealie|ghcr.io/mealie-recipes/mealie:latest|Up|1
|Audiobookshelf|ghcr.io/advplyr/audiobookshelf:latest|Up|1
|Heimdall|lscr.io/linuxserver/heimdall:latest|Up|1
|Gitea|gitea/gitea:latest|Up|1
|BookLore|booklore/booklore:latest|Up|1
|Jellyfin|Mjellyfin/jellyfin:latest|Up,transcoding failure|1|
|CUPS|ydkn/cups:latest|UP|1|
|Vaultwarden|vaultwarden/server:latest|Up, https dependent (Traefik)|1|
|PiHole|pihole/pihole:latest|Up|1|
|Immich|Photo/video management|Up|1|
|AdGuard Home|adguard/adguardhome|Crashed post initialization|2
|Grafana|grafana/grafana:latest|Up|2
|Prometheus|prom/prometheus:latest|Up|2
|Unbound|mvance/unbound:latest|Up|2
|Watchtower|containrrr/watchtower:latest|Up|2
|AlertManager|prom/alertmanager:latest|Up|2
|Uptime Kuma|louislam/uptime-kuma:1|Up|3
|Loki|	grafana/loki:latest|Up|3|
|Promtail|grafana/promtail:3.0.0|Up|3|
|Home Assistant|ghcr.io/home-assistant/home-assistant:stable|Up|3|
|dvwa|vulnerables/web-dvwa|Up|Lab|
|juiceshop|bkimminich/juice-shop|Up|Lab|
|webgoat|webgoat/webgoat-8.0|Up|Lab|
|WP Vuln|wordpress:5.1-apache|Up|Lab|
|Cyber Chef|mpepping/cyberchef:latest|Up|Lab|
|Kali Purple|iso|Up|Lab|


#### Down/Pending Containers

|Name|Role|Status|
|---|---|---|
|Monica|Personal CRM|down will run in own vm, possible db conflict|
|Syncthing|Continuous file synchronization|post migration|
|Syncthing|Synchronization target|post migration|
|pfSense|firewall & routing|post migration|
Security Onion|SOC suite (Zeek, Wazuh, Suricata, Elasticsearch)|SOC Node will be Up Pending Hardware|
TheHive|Incident response management|SOC Node will be Up Pending Hardware|
Cortex|Automated analysis engine|SOC Node will be Up Pending Hardware|
Seek|Log correlation and search|SOC Node will be Up Pending Hardware|
Velociraptor|EDR|Will be Up Absolutely last on SOC Node|
|Proxmox Backup Server|Cluster backup coordination|Up directly before Ansible|
|Ansible|Automation controller|Up directly before Traefik|
|Traefik|Reverse proxy / SSL| To be added Last, when network is secure, tested, and becomes public facing|
|Authentik|Unified Identity Provider IdP|up after Traefik
|Samba|File sharing for scanning/media|Reconsidering using Nextcloud WebDAV/on hold|
|Win 10 LTSC (VirtIO drivers)|Lightweight enterprize grade Windows OS|Post Lab machine replacement|
|Windows Server (Wuzah Agent & Winlogbeat installed)|Infrastructure OS (AD)|Post Lab machine replacement|

---

Environment: **Project Nyxline Home Lab**  
Report: **Application Configuration & Testing Field Report**  
