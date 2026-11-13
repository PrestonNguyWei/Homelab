# Media Server Backup and Recovery



## Overview



The media server currently uses multiple backup methods to protect the Proxmox environment and important configuration data.



Proxmox GUI backups are used to create recoverable copies of virtual machines and LXC containers. Important host and application configuration files are also backed up separately to both removable USB storage and Backblaze B2.



This provides multiple recovery options for system and configuration failures. However, the media library itself is currently not backed up, and the backup process has not yet been fully automated.



## Current Backup Strategy



The current backup strategy is divided into three primary layers:

* Proxmox GUI backups — Used to back up virtual machines and LXC containers.
* USB configuration backup — Stores a separate local copy of important host and application configuration data.
* Backblaze B2 configuration backup — Provides an offsite copy of important configuration and rebuild data.



These backup methods serve different purposes. Proxmox backups are primarily used for restoring workloads, while the USB and Backblaze copies provide additional protection for important configuration files required to rebuild or recover the environment.



The media library is currently excluded from the backup strategy because of its significantly larger storage requirements.



## What Is Backed Up



Proxmox GUI backups are used to preserve recoverable copies of selected virtual machines and LXC containers.



Important configuration and rebuild data is also backed up separately. This includes files and directories such as:



* `/etc/pve`
* `/etc/network/interfaces`
* `/etc/fstab`
* Docker Compose files
* `.env` files
* Other selected configuration paths required to rebuild the media-server environment



These files are copied to both removable USB storage and Backblaze B2.



## Backup Storage



### Proxmox Local Backups



The Proxmox host provides approximately 100 GB of local storage used for ISO images, container templates, and Proxmox backups.



These backups are useful for recovering workloads after container-level failures or configuration problems.



Because this storage resides on the same physical server as the active environment, it does not provide protection against complete host or storage failure.



### USB Backup



A removable USB drive is used to maintain an additional copy of important Proxmox, network, mount, and application configuration data.



Because this copy is stored separately from the active Proxmox storage, it provides an additional recovery source if the primary host configuration is damaged or unavailable.



### Backblaze B2



Important configuration and rebuild data is also backed up to Backblaze B2.



This provides an offsite copy of critical configuration files and reduces the risk of losing both the active environment and local backup copies in the same physical failure.



## Recovery Process



The recovery process depends on the type and severity of the failure.



### Container or VM Failure



If a virtual machine or LXC container becomes unusable, an available Proxmox GUI backup can be used to restore the affected workload.



After restoration, storage mount points, permissions, and application access should be verified before returning the service to normal operation.



### Configuration Failure



If important host or application configuration is damaged or lost, the required files can be recovered from either the USB backup or Backblaze B2.



Relevant configuration may include Proxmox configuration, network configuration, filesystem mount configuration, and Docker application configuration.



### Complete Host Failure



A complete Proxmox host failure would require rebuilding the host and restoring the required configuration and workloads.



The USB and Backblaze configuration backups provide copies of important rebuild information, while available Proxmox workload backups can be used to restore virtual machines and containers where those backups remain accessible.



Because the media library is not currently backed up, a complete loss of the media storage volume would result in loss of that media data.



## Failure Scenarios



### Container or Application Failure



An affected LXC container or virtual machine can be restored from a Proxmox GUI backup when an appropriate backup is available.



Because the media library is stored separately from container root filesystems, restoring an application container does not require restoring the entire media library.



### Configuration Error



Host or application configuration can be restored from the USB or Backblaze copies if a configuration file is accidentally changed, deleted, or corrupted.



### Mount or Permission Failure



If Jellyfin or the Docker-based media services lose access to the media library, the storage mount points and permissions should be checked before restoring an entire container.



### Complete Proxmox Host Failure



The local Proxmox backups may be unavailable if the entire physical host or its storage fails.



The USB and Backblaze configuration backups provide additional recovery material for rebuilding the environment, but workload and media recovery still depend on which data remains available.



### Media Volume Failure



The approximately 700 GB media volume is not currently backed up.



A complete failure of this storage would therefore result in loss of the media library and require the media to be recreated or reacquired.



## Limitations



The current backup architecture provides multiple copies of important configuration data, but it still has several limitations:

* The media library is not backed up.
* Backup jobs are not currently automated.
* Proxmox workload backups are stored on the same physical host.
* Local backup capacity is limited by the approximately 100 GB local storage.
* The USB backup depends on manual backup procedures.
* Recovery procedures have not yet been fully automated.



The USB and Backblaze copies improve resilience for important configuration data, but the current design does not yet provide full disaster recovery for all data stored by the media server.



## Planned Improvements



Future improvements may include:

* Automating Proxmox workload backups.
* Automating configuration backups to USB and Backblaze B2.
* Increasing off-host backup coverage.
* Implementing a backup strategy for important media data.
* Moving Proxmox backups to separate physical storage.
* Introducing a NAS or dedicated backup server.
* Maintaining multiple backup versions where appropriate.
* Regularly testing restoration procedures.
* Documenting a complete bare-metal Proxmox recovery process.



The long-term goal is to reduce reliance on manual backup steps and eliminate the current single-host dependency for workload backups.



