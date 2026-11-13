# Homelab



Documentation, configuration, and ongoing development of my Proxmox-based homelab.



This project began as a Windows-based server running Docker services and was later migrated to Proxmox after recurring host-level reliability issues. The current environment uses LXC containers, Docker workloads, shared storage, and multiple backup methods to support media services and future homelab expansion.



## Current Environment



The homelab currently includes:

* Proxmox VE as the virtualization platform
* LXC containers for isolated workloads
* Docker for containerized applications
* Jellyfin for media streaming
* Docker-based media management services
* Tailscale for remote connectivity
* Approximately 700 GB of dedicated media storage allocated from Proxmox `local-lvm`
* Proxmox workload backups
* Configuration backups stored on USB and Backblaze B2



## Documentation



Detailed documentation for the current media-server environment is available below:



* [Architecture](hosts/media-server/architecture.md)
* [Storage](hosts/media-server/storage.md)
* [Backup and Recovery](hosts/media-server/backup.md)
* [Troubleshooting](hosts/media-server/troubleshooting.md)



## Project Highlights



Some of the main problems addressed during the project include:

* Migrating the homelab from Windows to Proxmox after recurring update and restart-related outages
* Separating container root storage from bulk media storage
* Allocating and mounting a dedicated media volume from `local-lvm`
* Correcting media paths that were writing data to unintended storage
* Configuring container mount points and permissions for shared media access
* Implementing multiple backup locations for important configuration data
* Developing a structured troubleshooting process across host, storage, container, and application layers



## Current Limitations



The environment is still under active development.



Current limitations include:

* Media files are not currently backed up
* Some backup processes remain manual
* Proxmox workload backups are stored on the same physical host
* Additional storage redundancy has not yet been implemented



## Planned Improvements



Future improvements may include:

* Dedicated NAS or centralized storage
* Off-host Proxmox backups
* Automated backup workflows
* Improved monitoring and storage alerts
* Additional service documentation
* Sanitized Docker configuration examples
* Administrative and maintenance scripts



This repository is intended to document both the current state of the homelab and the troubleshooting, design decisions, and improvements made as the environment evolves.



