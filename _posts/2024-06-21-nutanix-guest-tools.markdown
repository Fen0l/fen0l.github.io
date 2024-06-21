---
layout: post
title: "Comprehensive Guide to Nutanix Guest Tools: Features, Benefits, and Deployment"
date: 2024-06-20 09:00:00
author: Anthony
categories: Nutanix
tags: Nutanix Guest-Tools VM-Management
cover: "/assets/banner/nutanix.png"

---

### Comprehensive Guide to Nutanix Guest Tools: Features, Benefits, and Deployment

#### Introduction to Nutanix Guest Tools (NGT)

Nutanix Guest Tools (NGT) is an essential suite of software utilities designed to enhance the management and functionality of virtual machines (VMs) on the Nutanix Acropolis Hypervisor (AHV). NGT offers features such as file-level restore, application-consistent snapshots, and seamless VM mobility, significantly improving VM management. It serves as the Nutanix equivalent of VMware Tools, but despite VMWare Tools beeing mandatory, NGTs are not necessary but recommended base on what features you would like to leverage from NGTs.

#### Key Benefits of Nutanix Guest Tools

- **File-Level Restore (FLR)**: Enables administrators to restore individual files within a VM without restoring the entire VM, optimizing time and resources.
- **Application-Consistent Snapshots**: Ensures applications within VMs are in a consistent state during snapshot creation, which is crucial for databases and transactional applications.
- **Seamless VM Mobility**: Facilitates easy VM migrations and cloning operations by integrating with Nutanix’s application mobility fabric.
- **Enhanced VM Management**: Provides advanced monitoring and management capabilities, including detailed insights into VM performance and health via Prism.

#### Components of Nutanix Guest Tools

- **Nutanix VM Mobility**
- **VirtIO**
- **Guest Agent**
- **Self Service Restore**
- **VSS Modules**
- **Checks and Prerequisites**
- **Guest Tools Infrastructure**

#### Prerequisites for Nutanix Guest Tools

- Prism Virtual IP must be configured.
- Network access from Prism Central to Guest VMs for IP-less setup.
- Network access from Guest VMs to Virtual IP for IP-based setup.
- Guest VMs must have an empty IDE or SATA CD-ROM for ISO attachment (required for installation, upgrades, or disaster recovery).
- TCP port 23578 on the guest VM must be accessible for VSS service utilization.

[More on Ports and Protocols](https://portal.nutanix.com/page/documents/list?type=software&filterKey=software&filterVal=Ports%20and%20Protocols&productType=Prism%20Central)

#### IP-Based vs IP-Less Communication

Nutanix Guest Tools (NGT) enables connectivity between the controller virtual machine (CVM) and guest VMs using either IP-based or IP-less communication.

- **IP-less Communication**: Utilizes a serial port connection between the NGA (Nutanix Guest Agent) and CVM.
- **IP-based Communication**: Uses SSL to connect NGA with CVM via the virtual IP on port 2074.

[More on IP-based Communication](https://portal.nutanix.com/page/documents/details?targetId=Prism-Central-Guide-vpc_2024_1:man-nutanix-guest-tool-nga-vm-communication-r.html)

![Guest-Tools-Service-CVM](/assets/nutanix/guest-tools-services.png)
![Guest-Tools-Agents](/assets/nutanix/guest-tools-agent.png)

#### Deploying Nutanix Guest Tools

**Architecture**:
NGT consists of client-side components installed within the guest VM and server-side components within the Nutanix cluster (NGT services on CVMs). These components communicate to deliver enhanced functionalities.

**Deployment and Configuration Workflow**:
1. **Prerequisites**:
   - Ensure Nutanix cluster and VMs are running supported AOS and AHV versions.
   - Maintain network connectivity between the Nutanix cluster and VMs where NGT will be installed.

2. **Enabling Nutanix Guest Tools**:
   - Log in to Prism.
   - Navigate to VM management.
   - Select the target VM(s).
   - Go to "Actions" -> "Guest Tools" -> "Enable".

3. **Configuring Nutanix Guest Tools**:
   - Post-installation, configure NGT settings within Prism.
   - Verify the NGT status for each VM in Prism.
   - Configure application-consistent snapshots using VSS for Windows or equivalent utilities for Linux.

4. **Using Nutanix Guest Tools**:
   - **File-Level Restore**:
     - Use the FLR CLI for self-service file-level recovery from VM snapshots.
     - In Prism, navigate to the VM and select the snapshot for file restoration.
   - **Application-Consistent Snapshots**:
     - Ensure VSS (Windows) or equivalent service (Linux) is running.
     - Create snapshots in Prism, selecting application consistency.
   - **VM Mobility**:
     - Use NGT for VM migration and cloning operations, ensuring minimal downtime and consistency.

From the Guest VM, the `ngtcli` command can list and restore snapshots. For Windows, a GUI is available for restore actions.

```bash
$ cd C:\Program Files\Nutanix\ngtcli; .\ngtcli.cmd  # Windows
$ /usr/local/nutanix/ngt/python36/python3 ngtcli.py # Linux
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

#### Additional Notes

- If a VM is cloned, NGT must be re-enabled on the cloned VM via Prism Element web console. If the VM is powered off, enable NGT and power on the VM. If powered on, enable NGT and restart the Nutanix Guest Agent service.
- Silent installation and automated deployment are supported in NGT versions 4.0 and 4.1.
- NGT certificates expire after 1000 days. To renew them:

```bash
nutanix@cvm$ ncc health_checks ngt_checks ngt_client_cert_expiry_check
nutanix@cvm$ nutanix_guest_tools_cli refresh_vm_tools_entity [vm_uuids=string-containing vm_uuid1,vm_uuid2....] [threshold_days=number-of-days]
```

Restart the Nutanix guest agent service:

- Linux: `$ sudo service ngt_guest_agent restart`
- Windows: `net stop "Nutanix Guest Tools Agent" && net start "Nutanix Guest Tools Agent"`

### Using Nutanix Guest Tools with Leap

When protecting a VM with Nutanix data protection, NGT information is included in the disaster recovery snapshot record. Upon migrating or restoring the VM, NGT information is preserved, and a new NGT ISO image is created for the recovered VM, automatically attaching to the VM for configuration.

If the VM is restored on a remote site or retrieved back to the local cluster, NGT functionality must be re-enabled.
---