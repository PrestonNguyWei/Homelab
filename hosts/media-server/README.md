# Proxmox Media Server



## Overview



The Proxmox Media Server is the primary system in my homelab and hosts infrastructure, remote-access, monitoring, and media-related services.



The server originally ran on Windows with Docker-based workloads but was later migrated to Proxmox VE after recurring host-level reliability issues. The current environment uses LXC containers and Docker workloads to separate services based on their purpose and resource requirements.



This is the first server in my planned homelab environment and continues to evolve as additional infrastructure is added.



## Current Role



The Proxmox Media Server currently provides:



* Virtualization using Proxmox VE
* LXC container hosting
* Docker container hosting
* Self-hosted media services
* DNS filtering
* Remote administration
* Remote network access
* Monitoring
* Local and offsite backup testing



## Current Platform



Hypervisor: Proxmox VE



Workload Types:

* LXC containers
* Docker containers
* Virtual machines when required



The majority of active services currently run inside LXC containers, with Docker hosted inside a dedicated LXC for applications that are easier to deploy and manage as containers.



## Current Services



### Infrastructure and Management

* Pi-hole: DNS filtering for the homelab
* Tailscale: Remote connectivity and subnet routing
* Docker: Containerized application hosting
* Portainer: Graphical management of the Docker environment
* Netdata: Host and service monitoring



### Media Services

* Jellyfin: Media streaming
* Navidrome: Music streaming
* Docker-based media management services: Media organization and supporting workloads



Jellyfin is hosted separately in its own LXC container, while supporting media-management applications run through Docker inside a dedicated Docker LXC.



## Storage



The media server uses Proxmox-managed storage with container root filesystems separated from the larger storage volume used for media data.



The host currently uses:

* `local` storage for ISO images, container templates, and Proxmox backups
* `local-lvm` for VM and LXC storage
* An approximately 700 GB virtual media volume allocated from `local-lvm`



The dedicated media volume is exposed to the appropriate containers through Proxmox-managed mount points.



For the complete storage design, troubleshooting history, and planned improvements, see:

[Storage Documentation](storage.md)



## Backups



The current backup strategy uses multiple methods for different recovery needs.



* Proxmox GUI backups are used for VM and LXC workload recovery
* Important configuration files are copied to removable USB storage
* Important configuration and rebuild data is also stored offsite in Backblaze B2
* The media library itself is not currently backed up



For the full backup strategy, recovery procedures, limitations, and planned improvements, see:

[Backup and Recovery](backup.md)



## Networking and Remote Access



Tailscale provides remote connectivity to the homelab.



A dedicated Tailscale LXC is configured for subnet routing so that authorized devices can access internal homelab resources remotely.



IPv4 and IPv6 forwarding are enabled on the Tailscale container to support subnet routing.



SSH is also used for remote administration where appropriate.



## Monitoring



Netdata is currently being used for host-level monitoring.



Additional monitoring and alerting are planned as the homelab expands, particularly for storage capacity, service availability, and backup status.



## Current Status



The media server is actively being developed.



Core services are operational, while additional improvements are still being implemented in areas such as:

* Backup automation
* Storage redundancy
* Monitoring and alerting
* Security controls
* Network segmentation
* Service redundancy



## Future Development



Planned improvements include:

* Dedicated NAS integration
* Off-host Proxmox backups
* Automated backup workflows
* Additional monitoring and alerting
* Improved security controls
* Network segmentation
* Service redundancy
* Additional physical homelab hosts
* Sanitized Docker configuration examples
* Administrative and maintenance scripts



## Documentation



Detailed documentation for the current media-server environment is available below:

* [Architecture](architecture.md)
* [Storage](storage.md)
* [Backup and Recovery](backup.md)
* [Troubleshooting](troubleshooting.md)



These documents describe the current architecture, design decisions, storage layout, recovery strategy, and troubleshooting experience as the environment continues to evolve.

