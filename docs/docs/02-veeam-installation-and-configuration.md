# 2. Installing and Configuring Veeam Backup & Replication

[← Previous: Lab Environment](01-lab-environment-and-architecture.md) | [Next: Running and Verifying Backups →](03-running-and-verifying-backups.md)

---

## Overview

With my lab environment ready, the next step was installing the software that would manage all my backups: **Veeam Backup & Replication Community Edition**. It is one of the most widely used backup products in enterprise environments, and the Community Edition is free for personal labs.

Installing Veeam is fairly straightforward. The real work begins afterwards. Decisions like where to store backups, how long to keep them and when jobs should run decide how reliable the backup system will be. Instead of clicking Next repeatedly, I took time to understand each option and why it mattered.

---

## 2.1 Downloading and Installing Veeam Community Edition

### Steps I followed

1. I visited the Veeam website and went to **Products > Veeam Backup & Replication**.
2. I selected the **Community Edition**, which required creating a free Veeam account.
3. After downloading the installation ISO (around 4 GB), I mounted it inside my **BackupServer** virtual machine.
4. I launched the installer by running `setup.exe`.
5. During installation I accepted the default components:
   - Veeam Backup Server
   - Backup Catalog
   - Veeam Console
6. I did **not** install Enterprise Manager, because this is a standalone lab and I would not use it.
7. I accepted the default installation location:

```text
   C:\Program Files\Veeam
```

8. For the configuration database I chose **SQL Server Express**, which Veeam installs automatically.
9. The installation took about fifteen minutes. When it finished, I restarted the BackupServer VM before opening Veeam for the first time.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The Veeam installer's program components page with Veeam Backup Server, Backup Catalog and Veeam Console ticked and Enterprise Manager not selected.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The installer's database engine page with Microsoft SQL Server Express selected and the installation folder set to `C:\Program Files\Veeam`.

### Why I chose SQL Server Express

| Question | Answer |
| -------- | ------ |
| **Why?** | My lab only has a handful of VMs, so a full SQL Server would add complexity for no benefit. |
| **What does it accomplish?** | Veeam stores backup jobs, restore points, schedules and configuration settings in this database. |
| **How does it work?** | The installer sets up SQL Express automatically on BackupServer and Veeam uses it behind the scenes. |
| **Production difference** | Enterprise environments protecting hundreds or thousands of VMs normally use a dedicated SQL Server. |

> [!TIP]
> Because the database holds all of Veeam's configuration, protecting it matters. I left BackupServer out of my backup job on purpose (see section 2.4) and noted that its own configuration database can be protected separately.

---

## 2.2 Adding My VMware Environment

Once Veeam was installed it did not know anything about my virtual machines. Before it could protect them, I had to tell it where they were running.

### Steps I followed

1. I opened the **Veeam Backup & Replication Console**.
2. In the left-hand menu I selected **Backup Infrastructure**, then **Managed Servers**.
3. I right-clicked inside the window and selected **Add Server**.
4. I added my VMware host using administrator credentials.

Organisations running VMware normally use **vCenter Server**, which lets Veeam manage many ESXi hosts from one place. My lab runs on VMware Workstation, so I could not use vCenter and connected to the VMware host directly.

After connecting, Veeam automatically discovered all the virtual machines in my lab:

- DC01
- Client01
- WebServer01
- BackupServer

> **📸 Screenshot Placeholder**
> Insert screenshot showing: Backup Infrastructure > Managed Servers in the Veeam console with the VMware host listed as connected and DC01, Client01, WebServer01 and BackupServer discovered beneath it.

### What this step proved

Seeing all four VMs appear inside Veeam was an important milestone. It confirmed that the backup server could talk to the environment it was going to protect.

| Question | Answer |
| -------- | ------ |
| **Why?** | Veeam has to know where workloads run before it can back them up. |
| **Production difference** | Enterprise environments usually connect Veeam to vCenter Server. My lab connects straight to the VMware host. |
| **What stays the same?** | The core backup concepts are exactly the same either way. |

---

## 2.3 Configuring the Backup Repository

The **backup repository** is the location where Veeam stores every backup it creates. Without one there is nowhere to save backup files.

### Steps I followed

1. I opened **Backup Infrastructure** and selected **Backup Repositories**.
2. I chose **Add Backup Repository**.
3. I selected **Direct Attached Storage**, because the backup drive was physically connected to the BackupServer.
4. I named the repository `HomeLabBackupRepo`.
5. I set the storage location to:

```text
   E:\VeeamBackups
```

   This folder is on my external 500 GB hard drive.
6. I set **Maximum Concurrent Tasks** to `2`.
7. I enabled **Decompress backup data blocks before storing**.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The New Backup Repository wizard with the name `HomeLabBackupRepo`, the storage location `E:\VeeamBackups`, Maximum Concurrent Tasks set to 2, and "Decompress backup data blocks before storing" ticked.

### Why I limited concurrent tasks to 2

| Question | Answer |
| -------- | ------ |
| **Why?** | My host only has 8 GB of RAM and several VMs were already running. |
| **What does it accomplish?** | Veeam can only process two jobs at a time. |
| **How does it work?** | If Veeam tried to back up every VM at once, it would put heavy pressure on CPU, memory and storage, making the whole lab slow and unstable. |
| **Trade-off** | I gave up a little backup speed in exchange for a more reliable system. |
| **What if I skipped it?** | The lab could slow to a crawl or become unstable while backups ran. |

### Why I enabled "Decompress backup data blocks before storing"

This uses a little more storage space, but it can make restores faster. Restore speed is exactly what matters in a disaster recovery situation.

---

## 2.4 Creating My Backup Job

A **backup job** tells Veeam:

- what to back up,
- where to store it,
- how often to run,
- and how many backups to keep.

I created **one backup job** that protected my three production VMs:

- DC01
- Client01
- WebServer01

I intentionally left **BackupServer** out of the job because it manages the backups itself. Its own configuration database can be protected separately.

### Backup job settings

| Setting | Value |
| ------- | ----- |
| Job name | `DailyBackup_AllProduction` |
| Protected VMs | DC01, Client01, WebServer01 |
| Repository | `HomeLabBackupRepo` |
| Retention | 7 restore points (one week of daily backups) |
| Backup method | Daily incremental backups |
| Full backup | Synthetic full backup every Sunday |
| Application-aware processing | Enabled |
| Guest file system indexing | Enabled, with administrator credentials entered for each VM |
| Schedule | Daily at 11:00 PM |

During the week, Veeam only backs up data that has changed since the previous backup. Every Sunday it automatically creates a new full backup *without* re-reading all the data from the VMs. This saves both storage space and backup time.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The New Backup Job wizard's Virtual Machines page with DC01, Client01 and WebServer01 selected and BackupServer excluded.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The Guest Processing page with "Enable application-aware processing" and "Enable guest file system indexing" both ticked and the guest OS credentials filled in.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The Schedule page of the backup job with the daily 11:00 PM run and the weekly synthetic full backup on Sunday.

### Why Application-Aware Processing was the most important setting

Out of everything I configured in Veeam, this was the setting I considered most important.

- **Without it**, Veeam captures the VM exactly as it is at that moment. Imagine pulling the power cable out of a computer while someone is saving a file. The computer stops instantly, and although most data survives, something may not have finished writing to disk. That is what a **crash-consistent** backup is like.
- **With it**, Windows tells running applications to finish writing their data properly *before* the snapshot is taken. This gives an **application-consistent** backup.

| Question | Answer |
| -------- | ------ |
| **Why?** | Services like Active Directory, databases and Microsoft Exchange can be damaged by a crash-consistent backup in ways that are not obvious until you restore. |
| **How does it work?** | Veeam talks to the guest operating system through Windows' Volume Shadow Copy Service (VSS), which prepares applications for a clean snapshot. This is why I had to enter guest credentials. |
| **What if I skipped it?** | The backup might still show success, but the restored domain controller could contain hidden corruption. |

> [!WARNING]
> This setting needs *administrator-level* guest credentials. In my first backup run of DC01, I used an ordinary domain user account and the job failed. The full story is in [Challenges and Troubleshooting](08-challenges-and-troubleshooting.md#challenge-1-my-first-backup-failed-because-of-vss-permissions).

---

## 2.5 Creating a Backup Copy Job

Creating backups is only part of a good backup strategy. If those backups are lost, corrupted or encrypted by ransomware, I could still end up with nothing. To reduce that risk I created a **Backup Copy Job**.

Instead of backing up the VMs again, this job copies backups Veeam has already created into another location.

### Settings I used

| Setting | Value |
| ------- | ----- |
| Target location | `E:\VeeamBackups\OffCopy` |
| Schedule | Every Sunday night, after the weekly synthetic full backup has finished |
| GFS retention: weekly | Keep weekly backups for 4 weeks |
| GFS retention: monthly | Keep monthly backups for 3 months |

```text
E:\VeeamBackups\OffCopy
```

Although this location is still on my external hard drive, it let me simulate an off-site backup location and understand how backup copies work.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The New Backup Copy Job wizard's GFS Retention page with "Enable GFS retention" ticked, weekly backups set to 4 weeks and monthly backups set to 3 months.

### Why I chose GFS retention

**GFS** stands for **Grandfather-Father-Son**. It keeps a mix of daily, weekly and monthly restore points.

At first, seven daily backups seemed like enough. Then I thought about a real scenario: *what if ransomware infected a server and nobody noticed for two weeks?* By then, all seven daily backups could already contain encrypted data, and rolling backups alone would not help.

GFS solves this by keeping older weekly and monthly copies. Even if a problem goes unnoticed for a long time, there is a much better chance of finding a clean backup from several weeks or months earlier.

| Question | Answer |
| -------- | ------ |
| **What if I skipped it?** | A slow-burning problem could silently overwrite every clean restore point. |
| **Production difference** | Organisations normally send the copy to cloud storage, tape or a second site. Mine stays on the same drive. |

---

## Visual Summary

![Section 2 summary: installing and configuring Veeam Backup & Replication](../images/02-install-and-configure-summary.png)

---

## What I Took Away From This Section

- Installing Veeam is the easy part. The value comes from how it is configured.
- Repositories, jobs and retention policies must match the environment and its resources.
- Application-aware processing is critical for consistent, reliable restores.
- Backup copy jobs and GFS retention protect against problems that go unnoticed.

---

[← Previous: Lab Environment](01-lab-environment-and-architecture.md) | [Next: Running and Verifying Backups →](03-running-and-verifying-backups.md)
