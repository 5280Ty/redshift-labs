# Proxmox Start Guide 

1. Open machine and document all hardware.  
2. Boot into Bios (F10)
3. Update Bios
4. Disable Extended Idle Power
5. Ensure Virtuaalization SVM (VT-x equivalent) is on.
6. Change boot order to USB
7. Plug in USB
8. Added static IP
9. Added fqdn (follow node naming conventions)
10. Reboot after loading.
11. Login 
12. On existing cluster node: get Join Information
13. On new node: paste info into Join Cluster in Web UI
14. Enter root password of the existing node
15. Wait for join to complete; refresh UI
16. Confirm with pvecm status and pvecm nodes
17. Ping ALL nodes in cluster. 
18. Switch to pve-no-subscription repository (Free)
19. confirm pveversion
20. nano /etc/apt/sources.list.d/pve-enterprise.sources. Comment out the existing enterprise line by adding a # at the beginning of the line.
21. nano /etc/apt/sources.list.d/pve-no-subscription.sources. add
```
Types: deb
URIs: http://download.proxmox.com/debian/pve
Suites: trixie
Components: pve-no-subscription
Signed-By: /usr/share/keyrings/proxmox-archive-keyring.gpg
```
22. mv /etc/apt/sources.list.d/pve-enterprise.sources \
   /etc/apt/sources.list.d/pve-enterprise.sources.disabled
23. nano /etc/apt/sources.list.d/ceph.sources
```
Types: deb
#URIs: https://enterprise.proxmox.com/debian/ceph-squid
URIs: http://download.proxmox.com/debian/ceph-squid
Suites: trixie
#Components: enterprise
Components: no-subscription
Signed-By: /usr/share/keyrings/proxmox-archive-keyring.gpg
```
24. apt update && apt upgrade
25. nano /etc/network/interfaces (document interfaces)
26. apt install ca-certificates curl (package should be up to date)
27. mkdir -p /etc/apt/keyrings
28. curl -fsSL https://download.docker.com/linux/debian/gpg \
  | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
29. chmod a+r /etc/apt/keyrings/docker.gpg
30.
```
tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: trixie
Components: stable
Signed-By: /etc/apt/keyrings/docker.gpg
EOF
```
31. apt clean
32. apt update
33. 
```
apt install -y \
  docker-ce \
  docker-ce-cli \
  containerd.io \
  docker-buildx-plugin \
  docker-compose-plugin
```
34.
```
systemctl enable docker
systemctl start docker
```


