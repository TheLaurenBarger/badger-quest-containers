# Badger Quest Container Quadlets

This repository serves as change controlled configuration for the Badger Quest services.

## Networks

The Badger Quest containers are seperated into operational stacks:

* Automation
* Development
* Media
* Operational

## Directory Structure

The following directory structure defines the quadlet definitions.

```bash
/etc/containers/systemd/
├── networks/
│   ├── media.network
│   ├── development.network
│   ├── automation.network
│   └── operational.network
└── containers/
    ├── media/
    │   ├── sonarr.container
    │   ├── prowlarr.container
    │   ├── jellyfin.container
    │   └── qbittorrent.container
    ├── development/
    │   ├── woodpecker.container
    │   ├── registry.container
    │   ├── maven-repository.container
    │   ├── mysql.container
    │   └── custom-java-app.container
    ├── automation/
    │   └── home-assistant.container
    └── operational/
        ├── nginx-proxy-manager.container
        ├── cloudflare-ddns.container
        ├── ntfy.container
        └── rabbitmq.container
```
