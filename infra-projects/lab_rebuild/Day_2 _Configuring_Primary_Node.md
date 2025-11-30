# Day 2 - Configuring the Primary Node  
**Author:** Ty  
**Date:** October 2025

Goal: To get all core services configured and fully operational.

---

## Primary Host: NoctHaven

| Setting|Description|Role|Status|
|---|---|---|---|
|Model|Dell OptiPlex 5050 SFF|Hypervisor and critical services|Active|
|CPU|Intel Core i5-7500 (4C/4T, QuickSync)|Transcoding and VM workloads|Stable|
|RAM|16 GB DDR4|Will be maxed later|Stable|
|Primary Storage|256 GB SSD|Proxmox OS and VM storage|Active|
|Secondary Storage |1 TB SSD|Photo and media library|Active|

QuickSync is available for Jellyfin hardware transcoding.

---

## Starting Compose File

```yml
services:
  db:
    image: mariadb:10.11
    container_name: nextcloud-db
    restart: unless-stopped
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW --log-bin=binlog --expire-logs-days=7
    environment:
      MYSQL_ROOT_PASSWORD: 'Redacted'
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: 'Redacted'
      MYSQL_PASSWORD: 'Redacted'
    volumes:
      - /mnt/dockerdata/mariadb:/var/lib/mysql

  nextcloud:
    image: nextcloud:apache
    container_name: nextcloud
    restart: unless-stopped
    depends_on:
      - db
    ports:
      - "8080:80"
    environment:
      MYSQL_HOST: db
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: 'Redacted'
      MYSQL_PASSWORD: 'Redacted'
    volumes:
      - /mnt/dockerdata/nextcloud/html:/var/www/html
      - /mnt/dockerdata/nextcloud/config:/var/www/html/config
      - /mnt/dockerdata/nextcloud/data:/var/www/html/data

  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: unless-stopped
    environment:
      WEBSOCKET_ENABLED: "false"
      ADMIN_TOKEN: 'Redacted'
    ports:
      - "8081:80"
    volumes:
      - /mnt/dockerdata/vaultwarden:/data

  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    restart: unless-stopped
    ports:
      - "8096:8096"
    volumes:
      - /mnt/dockerdata/jellyfin/config:/config
      - /mnt/dockerdata/jellyfin/cache:/cache
      - /mnt/ssdmedia:/media

  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: unless-stopped
    ports:
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /mnt/dockerdata/portainer:/data

  homer:
    image: b4bz/homer:latest
    container_name: homer
    restart: unless-stopped
    ports:
      - "8000:8080"
    volumes:
      - /mnt/dockerdata/homer:/www/assets
```

---


## Service Checks

### Homer (10.10.10.22:8000)

I reached the demo page, this app requires configuration. I located the real config directory via container inspection. Confirmed assets are mapped from `/mnt/dockerdata/homer`.

Actions taken:

* Stopped the container.
* Examined volume mappings with `docker inspect.
* Located existing config.yml .
* Adjusted permissions.
* Restarted the container.
* Confirmed Homer loads the correct config via logs.

### Nextcloud (10.10.10.22:8080)

* Logged in.
* Adjusted settings.
* Created users.
* Uploaded a test photo.
* Service confirmed operational.

### Vaultwarden (10.10.10.22:8081)

* Unresponsive.
* Requires additional configuration post Traefik install.

### Jellyfin (10.10.10.22:8096)

* Added users.
* Pointed libraries to 1 TB SSD.
* Uploaded full music library and several ripped movies.
* Playback tested on TV and phone successfully.

### Portainer (10.10.10.22:9000)

* Running normally.
* Sees local Docker instance, as expected.
* I will add new Nodes as they are installed.
* Other nodes will require the Portainer agent later.

---

## Homer Configuration Work

Commands executed:

```bash
docker stop homer
sudo mkdir -p /opt/homer/assets
sudo nano /opt/homer/assets/config.yml
sudo chown -R 1000:1000 /opt/homer/assets
sudo chmod -R 755 /opt/homer/assets
nano docker-compose.yml
```

I realized this was the wrong directory and validated the correct one under `/mnt/dockerdata/homer`.

Checked volume mappings:

```bash
docker inspect homer | grep -i '"Destination"' -A 4
```

Confirmed that `Destination` is `/www/assets`.

Examined contents of correct directory:

```bash
ls -l /mnt/dockerdata/homer
```

Found default files including `config.yml`, `config.yml.dist`, sample CSS, theme directory, and icons. I created a configuration yaml

```yml

title: "Nyxline Dashboard"
subtitle: "Redshift Protocol"
logo: "logo.png"

header: true
footer: '<p>Created with <span class="has-text-danger">❤️</span> by Ty</p>'

theme: default

colors:
  light:
    highlight-primary: "#3367d6"
    highlight-secondary: "#4285f4"
    highlight-hover: "#5a95f5"
    background: "#f5f5f5"
    card-background: "#ffffff"
    text: "#363636"
    text-header: "#ffffff"
    text-title: "#303030"
    text-subtitle: "#424242"
    card-shadow: rgba(0, 0, 0, 0.1)
    link: "#3273dc"
    link-hover: "#363636"

  dark:
    highlight-primary: "#3367d6"
    highlight-secondary: "#4285f4"
    highlight-hover: "#5a95f5"
    background: "#131313"
    card-background: "#2b2b2b"
    text: "#eaeaea"
    text-header: "#ffffff"
    text-title: "#fafafa"
    text-subtitle: "#f5f5f5"
    card-shadow: rgba(0, 0, 0, 0.4)
    link: "#3273dc"
    link-hover: "#ffdd57"

message:
  style: "is-dark"
  title: "Nyxline"
  icon: "fa fa-grin"
  content: "Welcome to the Redshift Protocol dashboard."

links:
  - name: "GitHub"
    icon: "fab fa-github"
    url: "https://github.com/"
  - name: "Wiki"
    icon: "fas fa-book"
    url: "https://www.wikipedia.org/"

services:
  - name: "Applications"
    icon: "fas fa-cloud"
    items:
      - name: "Portainer"
        icon: "fa-solid fa-server"
        url: "http://10.10.10.22:9000"
      - name: "Jellyfin"
        icon: "fa-solid fa-film"
        url: "http://10.10.10.22:8096"
      - name: "Nextcloud"
        icon: "fa-solid fa-cloud"
        url: "http://10.10.10.22:8080"
```


Final configuration steps:

```bash
chmod 644 /mnt/dockerdata/homer/config.yml
chmod -R 755 /mnt/dockerdata/homer
docker restart homer
docker logs homer
```

Logs confirmed serving correct configuration.

---

## Status Summary

* NoctHaven stable and fully operational.
* Nextcloud configured and live.
* Jellyfin misconfigured, media indexed, hardware transcoding supported, but not operating.
* Portainer functional.
* Homer pointing to correct config directory and loading properly.
* Vaultwarden pending configuration following reverse proxy installation.

More configuration work planned for Jellyfin and Vaultwarden.

---

## YAML Changes
### Docker Compose (Ending YAML)

```yml
services:
  db:
    image: mariadb:10.11
    container_name: nextcloud-db
    restart: unless-stopped
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW --log-bin=binlog --expire-logs-days=7
    environment:
      MYSQL_ROOT_PASSWORD: 'Redacted'
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: 'Redacted'
      MYSQL_PASSWORD: 'Redacted'
    volumes:
      - /mnt/dockerdata/mariadb:/var/lib/mysql

  nextcloud:
    image: nextcloud:apache
    container_name: nextcloud
    restart: unless-stopped
    depends_on:
      - db
    ports:
      - "8080:80"
    environment:
      MYSQL_HOST: db
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: 'Redacted'
      MYSQL_PASSWORD: 'Redacted'
    volumes:
      - /mnt/dockerdata/nextcloud/html:/var/www/html
      - /mnt/dockerdata/nextcloud/config:/var/www/html/config
      - /mnt/dockerdata/nextcloud/data:/var/www/html/data

  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: unless-stopped
    environment:
      WEBSOCKET_ENABLED: "false"
      ADMIN_TOKEN: 'Redacted'
    ports:
      - "8081:80"
    volumes:
      - /mnt/dockerdata/vaultwarden:/data

  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    restart: unless-stopped
    ports:
      - "8096:8096"
    volumes:
      - /mnt/dockerdata/jellyfin/config:/config
      - /mnt/dockerdata/jellyfin/cache:/cache
      - /mnt/ssdmedia:/media
# devices and environment added.
    devices:
      - /dev/dri/renderD128:/dev/dri/renderD128
    environment:
      TZ: America/Los_Angeles

  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: unless-stopped
    ports:
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /mnt/dockerdata/portainer:/data

  homer:
    image: b4bz/homer:latest
    container_name: homer
    restart: unless-stopped
    ports:
      - "8000:8080"
    volumes:
      - /mnt/dockerdata/homer:/www/assets
```

---

## Containers

### Total Running Containers

|Name|Image|Status|
|---|---|---|
|Homer|b4bz/homer:latest|Up, Healthy|
|Maria DB|mariadb:10.11|Up|
|Nextcloud|nextcloud:apache|Up|
|Portainer|portainer/portainer-ce:latest|Up|
|dvwa|vulnerables/web-dvwa|Up|
|juiceshop|bkimminich/juice-shop|Up|
|webgoat|webgoat/webgoat-8.0|Up|

### Down/Pending Containers

|Name|Role|Status|
|---|---|---|
|Audiobookshelf|Audiobooks & podcasts server|Post RAM Upgrade|
|Calibre|eBook management server|Post RAM Upgrade|
|Immich|Photo/video management|Post RAM Upgrade and/or transcoding fix|
|Jellyfin|Media server|Down for "Maint."|
|Samba|File sharing for scanning/media|Post RAM Upgrade|
|Syncthing|Continuous file synchronization|Up with node 3|
|Vaultwarden|Secure password vault|Inoperable until https|

---

## Next Steps

* Fix Vaultwarden availability and admin page access post Traefik.
* Customize Homer dashboard with icons and proper service tiles.
* Add Portainer Agent to additional hosts.
* Document networking layout for cross host Docker management.

---

## Reference

[jellyfin docs](https://jellyfin.org/docs/)  
[portainer docs](https://docs.portainer.io/)  
[vaultwarden docs](https://github.com/dani-garcia/vaultwarden)  
[homer docs](https://github.com/bastienwirtz/homer/tree/main/docs)  
[MariaDB docs](https://mariadb.com/docs/)  

---

Environment: **Project Nyxline Home Lab**  
Report: ** Primary Node Services Configuration**  
