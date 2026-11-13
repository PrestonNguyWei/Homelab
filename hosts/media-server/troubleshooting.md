# Media Server Troubleshooting



## Overview



Several issues encountered during the media-server deployment were related to platform reliability, storage allocation, mount points, permissions, and application access rather than failures of the applications themselves.



Documenting these problems provides a reference for diagnosing similar issues in the future and helps distinguish between host, container, storage, and application-level failures.



The troubleshooting process generally begins by identifying which layer of the environment is affected before making configuration changes or restoring a container.



## Windows Host Reliability



### Problem



The homelab originally ran on Windows using Docker containers, with Tailscale providing remote connectivity.



Windows automatic updates and restarts periodically caused the server and its hosted services to become unavailable. This affected services such as Pi-hole and also interrupted remote access to the system.



Although the applications themselves were functioning correctly, the underlying host platform was not providing the level of reliability desired for an always-on homelab.



### Investigation



Rather than continuing to troubleshoot each unexpected outage individually, the issue was evaluated at the host level.



The recurring outages showed that the problem was not limited to a single Docker container or application. The Windows host itself was creating an availability problem for services that were expected to remain online continuously.



Alternative homelab and virtualization platforms were researched to determine whether the environment could be structured more reliably.



### Resolution



Before changing the host operating system, the existing Windows configuration, Docker containers, and important service settings were documented so that the environment could be rebuilt if necessary.



The Windows installation was then replaced with Proxmox, and the services were redeployed into a virtualized environment using LXC containers and Docker where appropriate.



This allowed individual workloads to be separated and managed independently while providing a host platform designed around virtualization and server workloads.



### Result



After migrating to Proxmox, the recurring Windows update and restart issue no longer affected the availability of the homelab in the same way.



The migration also provided better separation between services and made it easier to manage storage, container resources, backups, and future expansion.



### Lesson Learned



The initial troubleshooting approach focused on keeping the existing Windows environment working, but the recurring outages demonstrated that the underlying platform itself should also be considered when diagnosing reliability problems.



Sometimes resolving a recurring problem requires reconsidering the overall system design rather than repeatedly addressing the immediate symptom.



## Media Files Filling the Wrong Storage



### Problem



During the initial media-server deployment, media files were being stored within the Docker container's root filesystem rather than on the larger storage intended for the media library.



The original root filesystem had approximately 40 GB of capacity, which was not sufficient for a growing media library.



### Cause



The media applications were using storage paths located within the container's root filesystem instead of a dedicated media volume.



### Resolution



Approximately 700 GB of storage was allocated from the Proxmox `local-lvm` storage and configured as a dedicated media volume.



The appropriate mount points and application paths were then configured so that media files could be stored on the larger volume rather than consuming the container's root filesystem.



### Lesson Learned



Application storage paths should be verified before large amounts of data are written.



Separating application and operating-system storage from bulk media storage prevents media growth from consuming the space required by the container itself.



## Media Written to the Wrong Proxmox Storage



### Problem



During storage configuration, media data was being written to the smaller Proxmox `local` storage instead of the larger media volume allocated from `local-lvm`.



Because `local` provides approximately 100 GB of capacity and is also used for ISO images, container templates, and Proxmox backups, media files caused its available space to decrease much faster than expected.



### Cause



The configured storage path did not direct the media data to the intended `local-lvm`-backed media volume.



### Resolution



The storage path and mount configuration were corrected so that media files were written to the approximately 700 GB media volume.



The smaller `local` storage could then remain dedicated to its intended Proxmox functions, while the media library remained on the larger storage volume.



### Lesson Learned



When disk usage increases unexpectedly, the first troubleshooting step should be to verify which filesystem is actually receiving the data rather than assuming the intended storage path is being used.



## Mount Point Issues



### Problem



The media storage volume needed to be accessible from multiple containers, including Jellyfin and the Docker LXC hosting media-management services.



Simply creating the larger storage volume did not automatically make it available to the applications inside those containers.



### Cause



The storage volume had to be exposed to the appropriate containers through Proxmox-managed mount points.



### Resolution



Mount points were configured so that the appropriate containers could access the shared media storage.



After the mount configuration was corrected, Jellyfin and the Docker-based media services could access the media library through their assigned paths.



### Troubleshooting Checks



When media storage is unavailable inside a container, verify:

* The media volume still exists and is available in Proxmox.
* The mount point is configured for the correct container.
* The expected storage path exists inside the container.
* The application is configured to use the mounted path rather than its root filesystem.
* The mount remains available after a container or host restart.



## Container Permission Issues



### Problem



After configuring the media volume and mount points, some services were unable to access the media files correctly.



### Cause



The containers and applications did not initially have the required permissions to access the mounted media storage.



### Resolution



The storage mount and permissions were adjusted so that the appropriate services could access the shared media library.



Jellyfin primarily requires access to read and stream media, while the Docker-based media-management services may require read and write access to manage files.



### Lesson Learned



A correctly mounted storage volume can still appear unavailable to an application if its permissions are incorrect.



Storage availability and file permissions should therefore be checked separately when diagnosing media-access problems.



## Jellyfin Media Access



If Jellyfin cannot detect or access media, the problem should be investigated from the storage layer upward before making major changes to the Jellyfin installation.



Recommended checks include:

1. Confirm that the media volume is available on the Proxmox host.
2. Confirm that the media mount is visible inside the Jellyfin container.
3. Verify that the expected media directories are present.
4. Check that Jellyfin has sufficient read access to the files and directories.
5. Verify that the library inside Jellyfin points to the correct mounted storage path.
6. Rescan the Jellyfin library after confirming that the underlying storage is available.



This helps distinguish between a Jellyfin configuration problem and a broader storage or mount-point problem.



## Docker Media Service Access



The Docker LXC hosts media-management services that interact with the same media library used by Jellyfin.



If a Docker-based service cannot access or modify media files, troubleshooting should include:

1. Confirming that the media volume is mounted inside the Docker LXC.
2. Verifying that the service is using the correct host and container paths.
3. Checking file and directory permissions.
4. Confirming that the service has the required read or write access.
5. Checking whether files are accidentally being written to the container root filesystem or another storage location.



Storage paths should be verified before modifying application configuration or rebuilding a container.



## Unexpected Storage Usage



Unexpected disk usage should be investigated by identifying which filesystem is actually growing.



Useful checks include:

* Available capacity on `local`.
* Available capacity on `local-lvm`.
* Container root filesystem usage.
* Media volume usage.
* Application download or media paths.
* Proxmox backup usage.



The earlier storage issues demonstrated that an application can appear to be using the correct media environment while actually writing data to a different filesystem.



Monitoring individual storage areas helps identify this problem before a smaller filesystem becomes full.



## General Troubleshooting Process



When a media-server service stops working correctly, troubleshooting should begin with the underlying infrastructure before making destructive changes.



A general troubleshooting order is:



1. Check Proxmox host status: Confirm that the host and affected container are running normally.
2. Check available storage: Verify that `local`, `local-lvm`, container root filesystems, and the media volume have sufficient free space.
3. Check the media volume: Confirm that the dedicated media storage exists and is available.
4. Check mount points: Verify that the correct media volume is mounted into the affected container.
5. Check paths: Confirm that the application is using the intended mounted storage path.
6. Check permissions: Verify that the application has the required read or read/write access.
7. Check the application: Only after confirming the storage environment should application-specific configuration or logs be investigated.
8. Consider restoration last: A Proxmox backup should generally be used only after determining that the issue cannot be corrected through normal configuration or storage troubleshooting.



This approach reduces the risk of rebuilding or restoring a container when the underlying problem is only a storage path, mount point, permission, or infrastructure issue.



