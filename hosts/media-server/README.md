# Proxmox Media Server



## Overview



The purpose of this server is to act as the main server in my stack and host critical applications such as Pi-Hole and Tailscale along with other important features later such as password management, while providing media hosting and streaming.



This is the first server in my planned homelab environment.



## Current Role



The Proxmox Media Server currently provides:

* Virtualization using Proxmox VE
* LXC container hosting
* Docker container hosting
* Self-hosted media services
* Remote administration
* DNS filtering
* Monitoring
* Local and offsite backup testing



## Current Platform



Hypervisor: Proxmox VE



Workload Types:

* LXC Containers
* Docker Containers
* Virtual Machines



## Current Services



Infrastructure and Management:

* Pi-hole
* Tailscale
* Docker
* Portainer
* Netdata



Media Services:

* Jellyfin
* Navidrome



## Storage



The server currently uses locally attached storage and Proxmox-managed storage volumes.

Additional storage configuration and a dedicated NAS are planned for future versions of the homelab.



## Backups



The current backup design is being developed around the 3-2-1 backup model:

* Three copies total of important configuration data
* Two different local storage copies
* One offsite copy



Current backup technologies being tested include:

* Proxmox VM/LXC backups
* Restic
* Removable local storage
* Backblaze B2 offsite storage



## Networking and Remote Access



Tailscale is used for remote connectivity to the homelab.

The current implementation includes subnet routing to allow authorized remote devices to reach internal homelab resources.



## Monitoring



Netdata is currently being evaluated for host-level monitoring.

Additional monitoring services are planned as the homelab expands.



## Current Status



This server is actively being developed.

Some services are operational, while others are still being configured, tested, or documented.



## Future Development



Planned improvements include:

* Backup automation
* Dedicated NAS integration
* Additional monitoring services
* Improved security controls
* Service redundancy
* Network segmentation
* Additional homelab hosts



## Documentation



Detailed documentation for individual services, networking, storage, backups, security, and troubleshooting will be added as the project develops.

