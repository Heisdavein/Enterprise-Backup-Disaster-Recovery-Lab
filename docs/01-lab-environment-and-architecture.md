# 1. My Lab Environment and Architecture

[← Back to README](../README.md) | [Next: Veeam Installation and Configuration →](02-veeam-installation-and-configuration.md)

---

## Why I Planned Before Building

Before I configured a single backup job, I wanted a well-planned environment. One thing I realised quickly while learning about backup and disaster recovery is that you cannot protect an environment you do not fully understand. Before thinking about backup schedules, recovery points or DR plans, I needed to know exactly:

- what systems I was protecting,
- where they were running,
- and how they all connected together.

Planning also made the rest of the project easier, because it gave me a clear picture of what needed backing up and helped me make decisions that reflect how real organisations design backup infrastructure.

---

## 1.1 Host Machine Specifications

| Component | Specification |
| --------- | ------------- |
| CPU | Intel Core i5 (4 cores) |
| RAM | 8 GB DDR4 |
| Storage | 256 GB SSD (primary) + 500 GB external HDD (backup target) |
| Operating system | Windows 11 |
| Virtualisation | VMware Workstation Player |
| Backup software | Veeam Backup & Replication Community Edition v12 |

My Windows 11 computer was the host for the entire lab. Every virtual machine ran through VMware Workstation, so this one physical computer powered everything in the project.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: Windows 11 Settings > System > About (or Task Manager > Performance) confirming the host's Intel Core i5 CPU and 8 GB of RAM.

### Decision: Store backups on a separate physical drive

I stored my backups on a **separate 500 GB external hard drive** instead of the SSD that held my virtual machines.

| Question | Answer |
| -------- | ------ |
| **Why did I do this?** | Keeping everything on one drive seemed easier at first, but it would defeat the purpose of having backups. |
| **What does it accomplish?** | If the SSD failed, became corrupted or was encrypted by ransomware, I would lose both my production VMs *and* every backup stored next to them. A separate drive means I still have an independent copy. |
| **How does it work?** | The Veeam repository points at a folder (`E:\VeeamBackups`) on the external drive, so Veeam writes backups there and never to the SSD. |
| **Why does it matter in production?** | It follows a core principle of backup design: never store your only backup on the same physical storage as the original data. Companies apply the same idea with separate storage arrays, other buildings or the cloud. |
| **What if I skipped it?** | One hardware failure, or one ransomware infection, could destroy the original data and the backups in a single event. |

> [!IMPORTANT]
> A backup that sits on the same disk as the data it protects is a convenience copy, not a disaster recovery plan.

---

## 1.2 Virtual Machines Protected in This Lab

| Virtual machine | Purpose |
| --------------- | ------- |
| **DC01** | Windows Server 2022: Domain Controller, DNS server and file server |
| **Client01** | Windows 11: domain-joined client workstation |
| **WebServer01** | Ubuntu Server 22.04: Apache web server |
| **BackupServer** | Windows Server 2022: Veeam Backup & Replication server |

To make the lab feel realistic I built a small environment of several VMs that each do a different job:

- **DC01** is the heart of the environment. It hosts Active Directory Domain Services (the system that stores user accounts and controls who can log in), DNS (which turns names into IP addresses) and file services. It is the most critical machine in the lab.
- **Client01** represents a normal employee workstation joined to the domain, so I could test user logins and recovery from a client's point of view.
- **WebServer01** is an Ubuntu server running Apache. I included it to show that Veeam protects Linux VMs alongside Windows ones.
- **BackupServer** is a completely separate VM that does nothing except manage backups.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The VMware Workstation Player library listing DC01, Client01, WebServer01 and BackupServer, with each VM's state visible.

### Decision: A dedicated backup server

I did not install Veeam on my domain controller or on any other server I was protecting. I created a separate VM called **BackupServer**.

| Question | Answer |
| -------- | ------ |
| **Why?** | It is considered best practice in enterprise environments. |
| **What does it accomplish?** | Keeping backup infrastructure separate from the systems being protected reduces the risk of one failure affecting everything at once. |
| **How does it work?** | Veeam runs on BackupServer, reaches the other VMs over the network, and writes backups to the external drive. The other machines do not hold the backup data. |
| **Why does it matter in production?** | One of the first things many attackers try after breaking into a network is to find and destroy the organisation's backups. If backups live on a compromised server, recovery becomes much harder. This idea is called **backup isolation**. |
| **What if I skipped it?** | An attacker or a failure on the domain controller could also take out the software and settings that manage recovery. |

> [!NOTE]
> My lab is far smaller than a real company network, but the design reflects the same approach many organisations use to make their backup infrastructure harder to compromise.

---

## 1.3 Backup Architecture Overview

| Component | Configuration |
| --------- | ------------- |
| Backup software | Veeam Backup & Replication Community Edition v12 |
| Backup type | VM-level image backups (the entire virtual machine) |
| Backup repository | External 500 GB USB hard drive (`E:\VeeamBackups`) |
| Backup schedule | Daily at 11:00 PM for all protected virtual machines |
| Retention policy | Seven restore points (one week of backups) |
| Backup job type | Incremental backups with periodic synthetic full backups |
| Backup copy job | Weekly copy to a secondary folder to simulate off-site storage |
| Network | VMware NAT (VMnet8) |

### What these settings mean

- **VM-level image backup:** Veeam captures the *whole* virtual machine: operating system, installed applications, settings and data. If a server is lost, I can restore the entire machine instead of rebuilding it from scratch.
- **Incremental backups:** after the first full backup, Veeam only saves what has changed since the last backup. This is much faster and uses much less storage.
- **Synthetic full backups:** every so often Veeam builds a new full backup from the data it already has, without re-reading every VM. I get fresh full backups without the cost of a full read.
- **Restore point:** one saved version of a machine that I can go back to. Seven restore points with daily backups gives me about a week of history.
- **Backup copy job:** a job that copies the backups Veeam already made to another location. It does not back up the VMs a second time.

### The 3-2-1 backup rule

I designed the lab around the **3-2-1 rule**:

- **3** copies of your data (the original plus two backups),
- on at least **2** different types of media,
- with **1** copy kept off-site or logically isolated from production.

| 3-2-1 requirement | How my lab approached it |
| ----------------- | ------------------------ |
| Original data | Production VMs on the host SSD |
| Backup 1 | Primary backups on the external hard drive |
| Backup 2 | The backup copy job, which writes to `E:\VeeamBackups\OffCopy` |
| Off-site / isolated copy | Simulated only (see the warning below) |

> [!WARNING]
> My backup copy lives on the **same external drive** as the primary backups. That means it does not give true off-site protection, and my lab only follows the 3-2-1 rule as closely as one computer allows. I used it to simulate the process organisations use when replicating backups to cloud storage, tape libraries or a second data centre. In production the copy would go to a separate location.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: Windows File Explorer on BackupServer showing the E: drive with the VeeamBackups folder and its OffCopy subfolder.

---

## Summary

Planning the lab this way taught me that an effective backup strategy is not just about making copies of data. It is about making sure those copies are still available when they are needed. That mindset shaped every decision I made in the rest of the project.

---

[← Back to README](../README.md) | [Next: Veeam Installation and Configuration →](02-veeam-installation-and-configuration.md)
