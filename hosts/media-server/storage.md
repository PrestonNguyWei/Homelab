\# Media Server Storage







\## Overview







The media server uses storage managed by the Proxmox host, with system and application storage separated from the larger storage capacity used for media files.







During the initial deployment, media files were stored within the Docker container's root filesystem. This approach quickly became unsuitable because the container had limited storage capacity and media files consumed significantly more space than the operating system and application data.







To address this, additional storage was allocated from a larger Proxmox storage pool and mounted into the appropriate containers. This provided approximately 700 GB of dedicated capacity for media while allowing the Docker container's root filesystem to remain focused on the operating system, applications, and configuration data.







\## Current Storage Model







The current storage design separates storage into two primary roles:







\* System and application storage — Used for storing container operating systems, installed applications, configuration files, and other application-related data.

\* Media storage — A separate, larger virtual storage volume used for media files.







The original container root filesystem was approximately 40 GB and was not intended to store a growing media library.







The Proxmox host also contained a significantly larger storage pool of approximately 900 GB. Approximately 700 GB of this capacity was allocated for media storage rather than expanding the Docker container's root filesystem.







This design keeps large media files separate from each container's primary filesystem and leaves additional host storage available for other virtual machines, containers, and future services.







\## Proxmox Storage







The Proxmox host uses two primary storage areas with different roles.







The `local` storage provides approximately 100 GB of capacity and is primarily used for ISO images, container templates, and Proxmox backups.







The `local-lvm` storage provides approximately 900 GB of capacity and is used for virtual machine and LXC container storage. The media server's approximately 700 GB virtual media volume is allocated from this `local-lvm` storage.







This separates Proxmox installation and recovery resources from the larger storage pool used by containers and the media library.







\## Container Storage







Each container maintains its own root filesystem for the operating system, applications, configuration files, and other relatively small pieces of application data.







Media files are not intended to be permanently stored within these root filesystems.







This separation became necessary after the original media deployment began consuming the container's limited disk capacity. Moving bulk media storage outside of the root filesystem prevented media growth from consuming space required by the container itself.







This approach also makes it easier to modify or rebuild application services without treating the media library as part of the container's operating system storage.







\## Mount Points







The media storage volume is created and managed by Proxmox and exposed to the appropriate containers through Proxmox-managed mount points. This allows Jellyfin and the Docker-based media services to access the shared media library without storing large media files within their individual root filesystems.







Configuring these mount points initially required additional troubleshooting because container permissions prevented the media services from accessing the storage correctly. After adjusting the mount configuration and permissions, the media library became accessible to the appropriate services.







\## Media Data Organization







Media data is kept separate from system and application files.







The dedicated media storage contains the library content consumed by media services, while application configuration and operating-system data remain within their respective container storage locations.







Media files are organized into separate directories based on content type, such as movies and television shows. This keeps the library easier to manage and allows applications to reference predictable storage locations.







The exact directory structure can be expanded as additional media types or services are introduced.







Keeping the media library centralized also prevents individual applications from creating separate copies of the same media across different container filesystems.







\## Permissions and Access







Storage configuration was one of the primary challenges encountered while implementing the media server.







During the initial setup, media files were being written to the smaller `local` storage instead of the larger media volume allocated from `local-lvm`. This caused the available space on the local storage to be consumed much faster than intended.







The final configuration corrected the storage path and mount setup so that media files are stored on the approximately 700 GB media volume backed by `local-lvm`, while the smaller `local` storage remains reserved for ISO images, container templates, and Proxmox backups.







Access permissions were then configured so that Jellyfin and the Docker-based media services could use the shared media storage as required.







\## Storage Maintenance







Storage maintenance focuses on preserving sufficient free capacity and ensuring that mounted storage remains accessible to the containers and applications that depend on it.







Routine storage checks include:



\* Monitoring available capacity on both the container root filesystem and media storage.

\* Verifying that the media storage mount is available after container or host restarts.

\* Checking file and directory permissions when applications cannot access media.

\* Avoiding unnecessary storage of large media files within the container root filesystem.

\* Verifying application access after storage or permission changes.

\* Documenting significant storage changes before modifying the existing configuration.







Keeping system storage and media storage separate also makes storage-related problems easier to diagnose because application or operating-system disk usage can be distinguished from growth of the media library.







\## Planned Improvements







The current storage configuration provides sufficient capacity for the existing homelab, but it is not intended to be the final storage architecture.







Future improvements may include:



\* Moving bulk media storage to dedicated storage hardware.

\* Introducing a NAS or other centralized storage system.

\* Increasing available media capacity as the library grows.

\* Improving redundancy for important data.

\* Implementing a more comprehensive backup and recovery strategy.

\* Further separating persistent application data from individual containers.

\* Improving storage monitoring and capacity alerts.







A dedicated NAS would allow the Proxmox host to focus primarily on compute workloads while providing centralized storage that could be accessed by multiple services.







Until that infrastructure is introduced, the current virtual storage configuration provides a practical separation between system storage and bulk media storage.







\## Storage Diagram







The storage architecture is documented visually in the following diagram:







!\[Media Server Storage Diagram](../../diagrams/media-server-storage.svg)







The diagram shows the relationship between the physical storage available to the Proxmox host, the Proxmox storage backends, the dedicated media volume, container mount points, and the applications that access the shared media library.





