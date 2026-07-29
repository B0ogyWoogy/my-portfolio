---
title: 'Building an Enterprise Active Directory Sandbox from Scratch'
description: 'A step-by-step guide to building an isolated Active Directory lab, configuring custom OUs, and enforcing Group Policies.'
pubDate: 'Jul 22 2026'
heroImage: '../../assets/blog-placeholder-4.jpg'
---

This document serves as a comprehensive engineering log and deployment blueprint for establishing an isolated Active Directory (AD) enterprise sandbox environment. It details the complete workflow from initial hypervisor setup and virtual switch configuration to directory schema creation, OU structure design, and Group Policy restriction enforcement.

## 💻 Phase 1: Hypervisor Architecture & VM Provisioning

Before configuring internal network settings or directory domains, the hardware virtualization layer was established to isolate guest kernels from the host machine.

### 1. Domain Controller Node (WIN-N064AKD5SSQ)

* **OS Environment:** Windows Server 2022 (64-bit)
* **Base Memory:** 4096 MB
* **vCPUs:** 2 Cores
* **Storage Profile:** 50.00 GB Dynamically Allocated Virtual Disk (VDI)

### 2. Workstation Client Node (DESKTOP-FQRKET4)

* **OS Environment:** Windows 10 Enterprise/Pro (64-bit)
* **Base Memory:** 4096 MB
* **vCPUs:** 2 Cores
* **Storage Profile:** 50.00 GB VDI attached to a virtual SATA Controller
* **Boot Order:** Floppy, Optical, Hard Disk

---

## 🔌Phase 2: Network Isolation & Interface Remediation

To bypass host-OS driver routing constraints and simulate an isolated physical copper switch, the virtual nodes were migrated from default NAT/Host-only setups to a software-defined Internal Network topology.

### 1. Virtual Switch Configuration

* **Attachment Mode:** 'Internal Network'
* **Switch Segment Name:** 'labnetwork'
* **Promiscuous Mode:** 'Allow All'
* **Link State:** 'Cable Connected'

### 2. Static IP Framework

Because private software switches lack a default DHCP daemon, static Layer-3 network parameters were declared manually:

| Property | Domain Controller ('WIN-N064AKD5SSQ') |Client Workstation (DESKTOP-FQRKET4) |
| :--- | :--- | :--- |
| **IPv4 Address** | '192.168.56.10' | '192.168.56.20'
| **Subnet Mask** | '255.255.255.0' | '255.255.255.0' |
| **Preferred DNS** | '127.0.0.1' (Loopback) | '192.168.56.10' (DC Target) |

### 3. Interface Reset & Link Refresh 

Network link states were reset using elevated PowerShell commands to flush historical ARP caches and update link states:

'''powershell
Disable-NetAdapter -Name "Ethernet" -Confirm:$false
Enable-NetAdapter -Name "Ethernet"'''

### Layer-3 & Layer-7 Validation

1. **ICMP Line-Speed Check:**

   ```text
   Pinging 192.168.56.10 with 32 bytes of data:
   Reply from 192.168.56.10: bytes=32 time<1ms TTL=128
   Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)

2. **DNS Resolver Verification:**

   'ipconfig /flushdns
   
   nslookup sandbox.local'

Resolver Output: 'Server: Unknown | Address: 192.168.56.10 | Name: sandbox.local'

## 👑 Phase 3: AD DS Promotion & Domain Integration1. 

### Domain Controller Promotion
1. Launched **Server Manager -> Add Roles and Features.** 
2. Installed **Active Directory Domain Services (AD DS)** and **DNS Server** roles along with administrative management consoles.
3. Promoted the server to a Domain Controller within a new forest named 'sandbox.local'.
4. Completed configuration and allowed the automated system reboot.

2. ### Workstation Domain Joining
1. Opened System Properties ('sysdm.cpl') on the Windows 10 workstation.
2. Toggled system membership from Workgroup to Domain with the string 'sandbox.local'.
3. Authenticated using domain administrative credentials ('sandbox.local\Administrator').**Validation:** Received prompt confirmation "Welcome to the sandbox.local domain" followed by an automated reboot.


📂 Phase 4: Directory Architecture & Account ProvisioningTo replicate enterprise directory hygiene and facilitate targeted Group Policy distribution, a custom Organizational Unit (OU) structure was mapped via Active Directory Users and Computers (dsa.msc).Directory Layout Treesandbox.local (Domain Root)
└── 📂 Corporate-HQ (Organizational Unit Container)
    ├── 📂 IT (Sub-OU)
    ├── 📂 HR (Sub-OU)
    └── 📂 Finance (Sub-OU)
Object & Permission MappingGlobal Security Group: Provisioned Finance-Depts-Clearance under the Corporate-HQ container.User Provisioning: Created domain user account Pam Sapp (sandbox.local\psapp) inside the HR sub-OU.Group Access Assignment: Added Pam Sapp directly to the Finance-Depts-Clearance group.🔒 Phase 5: Group Policy Architecture & GPO Deployment1. Policy InstantiationLaunched the Group Policy Management Console (gpmc.msc) on the DC.Linked a new GPO named Desktop-Restrictions-Policy directly to the Corporate-HQ root OU container.2. Restrictive Security ParametersNavigated through the administrative layout structure:User Configuration -> Policies -> Administrative Templates -> SystemPolicy Rule Entry: Prevent access to the command promptState Configuration: EnabledSub-parameter: Set Disable the command prompt script processing also? to No (Ensures logical logon/logoff scripts execute seamlessly while blocking shell access).🧪 Phase 6: Operational Enforcement & Testing1. Endpoint Policy SynchronizationLogged into the Windows 10 client machine using domain credentials (sandbox.local\psapp) and forced immediate policy payload retrieval from the domain controller's SYSVOL share:gpupdate /force
2. Execution Interception TestAttempting to execute cmd.exe from the Start Menu triggers immediate kernel interception referencing the pushed GPO registry parameters:Microsoft Windows [Version 10.0.19045.3803]
(c) Microsoft Corporation. All rights reserved.

The command prompt has been disabled by your administrator.

Press any key to continue . . .
Pressing any key terminates the system thread process instantly, demonstrating complete domain integration and structural security management success.🎯 System Operations Check: Active Directory Domain Controller, Internal Virtual Switch, DNS Resolution, User/Group Provisioning, and GPO Cascading Infrastructure are 100% Operational..
