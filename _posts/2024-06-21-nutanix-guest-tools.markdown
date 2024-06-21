---
layout: post
title: "Nutanix Guest Tools: Overview, Importance, and Implementation"
date: 2024-06-20 09:00:00
author: Anthony
categories: Nutanix
tags: Nutanix Guest-Tools VM-Management
cover: "/assets/banner/nutanix.png"
---

### Nutanix Guest Tools: Overview, Importance, and Implementation

#### Overview of Nutanix Guest Tools (NGT)

Nutanix Guest Tools (NGT) is a suite of software utilities provided by Nutanix to enhance the functionality and management of virtual machines (VMs) running on the Nutanix Acropolis Hypervisor (AHV). NGT provides features such as file-level restore, application-consistent snapshots, and seamless VM mobility, improving the overall VM management experience. It is an equivalent of VMware Tools.

#### Importance of Nutanix Guest Tools

- **File-Level Restore**: Allows administrators to restore individual files within a VM without needing to restore the entire VM, saving time and resources.
- **Application-Consistent Snapshots**: Ensures that applications within VMs are in a consistent state during snapshot creation, critical for databases and other transactional applications.
- **Seamless VM Mobility**: Facilitates easier VM migrations and cloning operations by integrating with Nutanix’s application mobility fabric.
- **Enhanced VM Management**: Provides improved monitoring and management capabilities for VMs, including better visibility into VM performance and health from Prisms instances.

#### Nutanix Guest Tools Packages

- Nutanix VM Mobility
- VirtIO
- Guest Agent
- Self Service Restore
- VSS Modules
- Checks and prerequisites
- Guest Tools Infra

#### Nutanix Guest Tools Requirements

- Prism Virtual IP configured
- Network access from Prism Central to Guest VMs for IP-less
- Network access from Guest VM to Virtual IP for IP-based
- Guest VM must have an empty IDE or SATA CD-ROM (attach ISO for install, upgrade, or in case of DR)
- The TCP port 23578 in the guest VM must be accessible if you want to use the VSS service.

[More on Ports and Protocols](https://portal.nutanix.com/page/documents/list?type=software&filterKey=software&filterVal=Ports%20and%20Protocols&productType=Prism%20Central)

##### IP-Based vs IP-Less

You can set up connectivity between the controller virtual machine (CVM) and the guest VMs using the Nutanix Guest Tools (NGT) service that runs on the CVM and the Nutanix Guest Agent (NGA) service that runs on the guest VMs in the Nutanix cluster. After you install NGT in a guest VM, the NGA service in the guest VM starts periodic communication with the CVM based on the type of communication in a VM:

- **IP-less**: Communication between NGA and CVM happens only over a serial port connected to the guest VM.
- **IP-based**: NGA communicates with the CVM over SSL by connecting to the virtual IP of the CVM on port 2074.

[More on IP-based Communication](https://portal.nutanix.com/page/documents/details?targetId=Prism-Central-Guide-vpc_2024_1:man-nutanix-guest-tool-nga-vm-communication-r.html)

![Guest-Tools-Service-CVM](/assets/nutanix/guest-tools-services.png)

![Guest-Tools-Agents](/assets/nutanix/guest-tools-agent.png)

#### Implementation of Nutanix Guest Tools

**Architecture**:
NGT is composed of client-side components installed within the guest VM and server-side components integrated into the Nutanix cluster (NGT services running on CVMs). These components communicate to provide the enhanced functionalities.

**Deployment and Configuration Workflow**:
1. **Prerequisites**:
   - Ensure Nutanix cluster and VMs are running a supported version of AOS and AHV.
   - Network connectivity between the Nutanix cluster and the VMs where NGT will be installed (IP-based service exposed over TCP/2074 on the guest VM and IP-less feature coming in AOS 6.8).
2. **Enabling Nutanix Guest Tools**:
   - Log in to the Prism interface.
   - Navigate to the VM management section.
   - Select the target VM(s) for which you want to enable NGT.
   - Click on "Actions" -> "Guest Tools" -> "Enable".
3. **Configuring Nutanix Guest Tools**:
   - Post-installation, configure NGT settings within Prism.
   - Verify the NGT status for each VM in Prism (should show as enabled and running).
   - Configure application-consistent snapshots by integrating with the guest OS's VSS (Volume Shadow Copy Service) for Windows or similar utilities for Linux.
4. **Using Nutanix Guest Tools**:
   - **File-Level Restore**:
     - File Level Restore (FLR) CLI. Performs self-service file-level recovery from the VM snapshots.
     - In Prism, navigate to the VM and select the snapshot from which you want to restore files.
     - Use the file-level restore feature to browse and restore specific files.
   - **Application-Consistent Snapshots**:
     - Ensure the VSS service (Windows) or appropriate service (Linux) is running.
     - Supports application-consistent snapshots for Linux VMs by running specific scripts on VM quiesce.
     - Create snapshots from Prism, selecting the option for application consistency.
   - **VM Mobility**:
     - Use NGT to assist with VM migration and cloning operations, ensuring minimal downtime and consistent states across nodes and clusters.

From the Guest VM, using the `ngtcli` command installed in ProgramFiles, we can list and restore snapshots. For Windows, there is a GUI that can handle all restore actions. Running Nutanix SSR Software from the Guest VM:

```bash
$ cd C:\Program Files\Nutanix\ngtcli; .\ngtcli.cmd  # for Windows
$ /usr/local/nutanix/ngt/python36/python3 ngtcli.py # for Linux
$ ssr ls-snaps
$ ssr attach-disk disk-label=<disk_label> snapshot-id=<snapshot_id>
$ ssr detach-disk attached-disk-label=<attached_disk_label>
```

#### Technical Diagram of NGT Workflow

**NGT Deployment Workflow**:

```plaintext
1. Enable NGT in Prism
   +--------------------------------------+
   | Prism Interface                      |
   | - Select VM                          |
   | - Actions -> Guest Tools -> Enable   |
   +--------------------------------------+
               |
2. Install NGT in Guest OS
   +--------------------------------------+    +-----------------------------------+
   | Windows VM                           |    | Linux VM                          |
   | - Download and run installer         |    | - Download and extract tarball    |
   | - Follow prompts and reboot          |    | - Run installation script         |
   +--------------------------------------+    +-----------------------------------+
               |
3. Configure NGT in Prism
   +--------------------------------------+
   | Prism Interface                      |
   | - Verify NGT status                  |
   | - Configure settings                 |
   +--------------------------------------+
               |
4. Utilize NGT Features
   +--------------------------------------+    +-----------------------------------+
   | File-Level Restore                   |    | Application-Consistent Snapshots  |
   | - Navigate to VM snapshot            |    | - Ensure VSS service running      |
   | - Browse and restore files           |    | - Create snapshot in Prism        |
   +--------------------------------------+    +-----------------------------------+
```

#### Notes

- If you clone a VM, NGT is not enabled on the cloned VM by default. If the cloned VM is powered off, enable NGT from the Prism Element web console and power on the VM. If the cloned VM is powered on, enable NGT from the Prism Element web console and restart the Nutanix Guest Agent service.
- Silent install provided and ability to automate deployment using the latest 4.0 and 4.1 NGT versions.
- NGT certificates expire after 1000 days. To renew them:

```bash
nutanix@cvm$ ncc health_checks ngt_checks ngt_client_cert_expiry_check
nutanix@cvm$ nutanix_guest_tools_cli refresh_vm_tools_entity [vm_uuids=string-containing vm_uuid1,vm_uuid2....] [threshold_days=number-of-days]
```

Restart the Nutanix guest agent service by running the `$ sudo service ngt_guest_agent restart` command on Linux VMs. For Windows VMs, in the command prompt, run `net stop "Nutanix Guest Tools Agent" && net start "Nutanix Guest Tools Agent"`.

### NGTs with Leap

If a VM that is protected by the Nutanix data protection feature and has NGT enabled for it or vice-versa, the relevant NGT information including capabilities is added as part of the disaster recovery snapshot record.

If you migrate a VM to a remote site, restore it in place, or clone it from a snapshot, the NGT information is preserved for the restored VM. A new NGT ISO image containing only the relevant configuration information (SSL certificates, Controller VM IP address, etc.) is created for the recovered VM and the image is automatically attached to the VM.

When the VM is powered on, the NGA Nutanix Guest Agent service running on the VM copies the relevant configuration information and detaches the CD-ROM automatically.

If you restore a VM on the remote site or retrieve the snapshot back to the local or source cluster, NGT information or functionality is lost. Therefore, you must enable NGT again for the restored VM.

### Nutanix Guest Tools Troubleshooting Guide

- TBD