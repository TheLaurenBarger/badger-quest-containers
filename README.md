# Quadlets Repository Structure Example

This document shows how to organize your quadlets repository with directories per service category.

## Repository Structure

```bash
quadlets-repo/
├── media/                     # Media servers and streaming
├── download/                  # Download clients
├── *arr/                      # Media management (*arr stack)
├── monitoring/                # System monitoring
├── networking/                # Network services
├── shared/                    # Shared networks and volumes
├── backup/                    # Backup services
├── home-automation/           # Home automation services
├── docs/                      # Documentation
└── scripts/                   # Helper scripts
```

## Service Categories

### Media Services (`media/`)

- `jellyfin.container` - Media server with hardware transcoding
- `plex.container` - Alternative media server
- `emby.container` - Another media server option
- `navidrome.container` - Music streaming server

### Download Clients (`download/`)

- `transmission.container` - BitTorrent client
- `qbittorrent.container` - Alternative BitTorrent client
- `sabnzbd.container` - Usenet downloader
- `nzbget.container` - Alternative usenet client

### Media Management (`*arr/`)

- `sonarr.container` - TV show management
- `radarr.container` - Movie management
- `lidarr.container` - Music management
- `prowlarr.container` - Indexer management
- `bazarr.container` - Subtitle management
- `readarr.container` - Book/audiobook management

### Monitoring (`monitoring/`)

- `grafana.container` - Metrics visualization
- `prometheus.container` - Metrics collection
- `node-exporter.container` - System metrics
- `cadvisor.container` - Container metrics
- `uptime-kuma.container` - Uptime monitoring

### Networking (`networking/`)

- `traefik.container` - Reverse proxy
- `nginx-proxy-manager.container` - Alternative proxy
- `wireguard.container` - VPN server
- `pihole.container` - DNS ad-blocker

### Shared Resources (`shared/`)

- `media-network.network` - Shared container network
- `media-volume.volume` - Shared storage volume
- `database-volume.volume` - Database storage

### Backup Services (`backup/`)

- `duplicati.container` - Backup solution
- `restic.container` - Alternative backup
- `borgmatic.container` - Borg backup wrapper

### Home Automation (`home-automation/`)

- `home-assistant.container` - Home automation hub
- `mosquitto.container` - MQTT broker
- `zigbee2mqtt.container` - Zigbee bridge
- `node-red.container` - Flow-based automation

## File Naming Conventions

- **Container files**: `servicename.container`
- **Network files**: `networkname.network`
- **Volume files**: `volumename.volume`
- **Pod files**: `podname.kube` (for Kubernetes YAML)

## Example Files

### Media Server with GPU Access

**`media/jellyfin.container`:**
```ini
[Unit]
Description=Jellyfin Media Server with Intel QuickSync
After=network-online.target media-network.service
Wants=network-online.target
Requires=media-network.service

[Container]
Image=docker.io/jellyfin/jellyfin:latest
PublishPort=8096:8096
Volume=/var/container/data:/media:ro
Volume=/var/container/config/jellyfin:/config:Z
Volume=/var/container/cache/jellyfin:/cache:Z
Device=/dev/dri:/dev/dri
Environment=JELLYFIN_PublishedServerUrl=http://192.168.1.100:8096
Environment=NVIDIA_VISIBLE_DEVICES=all
Environment=NVIDIA_DRIVER_CAPABILITIES=compute,video,utility
Network=media-net
User=badger
Group=badger
AddDevice=/dev/dri/renderD128

[Service]
Restart=always
TimeoutStartSec=900

[Install]
WantedBy=multi-user.target default.target
```

### Shared Network Definition

**`shared/media-network.network`:**
```ini
[Unit]
Description=Media Services Internal Network

[Network]
NetworkName=media-net
Internal=false
Subnet=172.20.0.0/16
Gateway=172.20.0.1
DNSEnabled=true
```

### Download Client with VPN

**`download/transmission.container`:**
```ini
[Unit]
Description=Transmission BitTorrent Client
After=network-online.target wireguard.service
Wants=network-online.target
Requires=wireguard.service

[Container]
Image=docker.io/linuxserver/transmission:latest
PublishPort=9091:9091
Volume=/var/container/config/transmission:/config:Z
Volume=/var/container/data/downloads:/downloads:Z
Volume=/var/container/data/watch:/watch:Z
Environment=PUID=1000
Environment=PGID=1000
Environment=TZ=UTC
Environment=TRANSMISSION_WEB_HOME=/kettu/
Network=container:wireguard
User=badger
Group=badger

[Service]
Restart=always
TimeoutStartSec=900

[Install]
WantedBy=multi-user.target default.target
```

### Monitoring Stack

**`monitoring/grafana.container`:**
```ini
[Unit]
Description=Grafana Metrics Dashboard
After=network-online.target prometheus.service
Wants=network-online.target

[Container]
Image=docker.io/grafana/grafana:latest
PublishPort=3000:3000
Volume=/var/container/config/grafana:/var/lib/grafana:Z
Environment=GF_SECURITY_ADMIN_PASSWORD=admin
Environment=GF_USERS_ALLOW_SIGN_UP=false
Network=media-net
User=badger
Group=badger

[Service]
Restart=always
TimeoutStartSec=900

[Install]
WantedBy=multi-user.target default.target
```

### Automated Deployment

The CoreOS system will automatically:

1. Pull the latest changes from your repository
2. Find all `*.container`, `*.network`, `*.volume`, and `*.kube` files
3. Copy them to `/etc/containers/systemd/`
4. Reload systemd and restart services

This structure makes it easy to manage complex home lab setups with dozens of services while keeping everything organized and maintainable.
