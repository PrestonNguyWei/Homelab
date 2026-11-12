# Media Server Architecture



## Overview



The Proxmox Media Server is the primary system in my homelab and acts as the main virtualization platform for infrastructure, media, remote access, monitoring, and backup-related services.



The server uses Proxmox VE to separate workloads between LXC containers and Docker containers. Services are separated based on their purpose and resource requirements while still allowing them to communicate where necessary.



This document describes how the current Media Server is structured and how its major components interact.



## Physical Host



The Media Server runs Proxmox VE directly on a dedicated physical system.



The physical host provides the CPU, memory, storage, and network resources used by the LXC containers and other workloads running under Proxmox.



Detailed hardware specifications for the current Media Server will be documented separately once they are fully recorded.



## Proxmox VE



Proxmox VE acts as the main virtualization and management platform for the server.



Proxmox is responsible for:

* Hosting and managing LXC containers
* Supporting virtual machines when required
* Allocating CPU, memory, and storage resources
* Managing container storage volumes
* Providing local backup functionality
* Managing the virtual networking used by hosted workloads



The majority of the current Media Server services are hosted using LXC containers because they provide lightweight isolation while requiring fewer resources than full virtual machines.



## LXC Containers



LXC containers are used to separate major services and groups of services.



The current architecture includes dedicated containers for services such as:

* Pi-hole
* Tailscale
* Jellyfin
* Docker



Pi-hole provides DNS filtering for the environment.



Tailscale provides remote connectivity and subnet routing for authorized devices.



Jellyfin is hosted separately as a dedicated media streaming service for GPU passthrough.



Docker is hosted inside its own LXC container and is used to run applications that are more easily managed as Docker containers.



This structure allows individual services to be managed and backed up separately while limiting the impact that problems with one service may have on the rest of the server.



## Virtual Machines



Virtual machines are available for workloads that require stronger isolation, a different operating system, or functionality that is not well suited for an LXC container.



The current Media Server primarily relies on LXC and Docker containers for its active services.



## Docker Environment



Docker runs inside a dedicated LXC container.



Portainer is installed to provide a graphical management interface for the Docker environment.



The Docker environment hosts containerized applications that benefit from centralized deployment and management.



Applications use shared storage paths where required so that related services can work with the same application and media data.



A Docker network is also used to allow related applications to communicate with one another internally.



## Storage Architecture



The Media Server currently uses locally attached storage and Proxmox-managed storage volumes.



Additional storage volumes can be created through Proxmox and mounted to containers that require additional capacity.



Containerized applications use shared storage paths where multiple services require access to the same application or media data.



A dedicated NAS is planned for a future version of the homelab. The NAS will eventually separate centralized storage from the Media Server itself.



## Networking



Proxmox provides the virtual networking required for containers and services to communicate with the local network.



Individual services communicate either through the Proxmox network or through Docker networks depending on where the application is hosted.



Pi-hole provides DNS filtering services for the environment.



The Docker applications also uses an internal Docker network to allow related containers to communicate without requiring every service to communicate externally.



## Remote Access



Tailscale is used to provide secure remote access to the homelab.



A Tailscale LXC is configured to provide subnet routing, allowing authorized Tailscale devices to reach internal homelab resources while away from the local network.



IPv4 and IPv6 forwarding are enabled on the Tailscale container to support this functionality.



SSH can also be used for remote administration of supported systems when connected through an authorized network path.



## Backup Architecture



The current backup architecture is being developed around the 3-2-1 backup model.



The current design includes:

* Proxmox VM and LXC backups
* Restic for additional backup management
* Removable local storage
* Backblaze B2 for offsite storage



Proxmox backups are used to preserve VM and LXC workloads.



Restic is being used to protect important configuration files and backup data required to rebuild the environment.



Important configuration targets include Proxmox configuration, network configuration, mount configuration, and Proxmox backup archives.



Bulk media and other large datasets will use separate storage and recovery methods rather than being included in every core configuration backup.



## Current Architecture Diagram



!\[Proxmox Media Server Architecture](../../diagrams/media-server-architecture.svg)



## Planned Changes



The Media Server architecture will continue to change as the larger homelab is developed.



Planned improvements include

* Dedicated NAS integration
* Improved backup automation
* Additional monitoring
* Improved security controls
* Network segmentation
* Service redundancy
* Migration of some critical services to redundant systems
* Additional physical homelab hosts



As these systems are deployed, the architecture documentation and diagrams will be updated to reflect the current environment.

