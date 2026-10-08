# 3. Running Backups and Verifying Their Integrity

[← Previous: Installation and Configuration](02-veeam-installation-and-configuration.md) | [Next: Full and File-Level Restores →](04-full-and-file-level-restores.md)

---

## Overview

Creating a backup job is only the beginning. Running it is the next step. The part that really matters is **proving the backup works**.

While researching backup and disaster recovery, I read about organisations that believed their backups were working and then found, during an emergency, that they had been failing silently for weeks or months. By then it was too late.

That changed how I thought about backups. I did not want to create a job, see a green tick and assume everything was fine. I wanted evidence that my backups could restore a system when it mattered. So after creating my jobs I focused on running them, understanding what Veeam was doing behind the scenes, and verifying that every restore point was usable.

---

## 3.1 Running My First Backup

### Steps I followed

1. In the Veeam Backup & Replication console, I located the `DailyBackup_AllProduction` job.
2. I right-clicked the job and selected **Start**.

The backup started almost immediately. In the bottom panel of the console I could watch each VM move through the stages of the backup in real time. Because I had limited the job to two concurrent tasks, Veeam processed two VMs at a time and then moved on to the third.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The Veeam console with the `DailyBackup_AllProduction` job running and the job progress panel showing the per-VM stages.

### What Veeam does for every VM

| Stage | What I learned |
| ----- | -------------- |
| Creating snapshot | Veeam asks VMware to create a snapshot of the VM, capturing the exact state of the disk at that moment. |
| Processing VSS | The Windows Volume Shadow Copy Service (VSS) prepares running applications so the backup is consistent. |
| Transferring data | Veeam copies the VM data from the snapshot into the backup repository. |
| Committing snapshot | Once the data has been copied, VMware removes the snapshot and merges any new changes back into the original virtual disk. |
| Indexing guest files | Veeam indexes the files inside the operating system, which makes it easier to restore individual files later. |
| Completing | The backup finishes and a new restore point is created. |

### Result of the first (full) backup

| Measure | Result |
| ------- | ------ |
| Backup type | Full backup (Veeam copied every bit of data from each protected VM) |
| Duration | About 35 minutes |
| Storage used | Approximately 28 GB |
| Final status | All three VMs showed a green **Success** status and their first restore point |

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The completed first backup with all three VMs showing Success, along with the duration (about 35 minutes) and the 28 GB backup size.

### What this taught me about snapshots

I used to think a VM somehow paused while it was being backed up. That is not what happens.

- VMware creates a **snapshot** (a frozen, point-in-time view of the disk).
- Veeam reads data from the snapshot while the VM **keeps running normally**.
- Any new changes are temporarily written to a separate **delta file**.
- When the backup finishes, VMware merges those changes back into the main virtual disk.

From a user's point of view nothing changes. They can keep working without knowing a backup is taking place. This is how modern backup software protects live systems without downtime.

---

## 3.2 Running an Incremental Backup

The next day I wanted to see how an **incremental backup** behaves. Unlike a full backup, it saves only data that changed since the previous backup.

### Changes I made first

Before running the job again I deliberately changed each VM:

| VM | Change |
| -- | ------ |
| DC01 | Created several new files |
| WebServer01 | Modified a few configuration files |
| Client01 | Updated some user account settings |

Then I ran the same backup job again.

### Full vs incremental

| Backup | Duration | Storage used |
| ------ | -------- | ------------ |
| Full backup (day 1) | About 35 minutes | About 28 GB |
| Incremental backup (day 2) | About 6 minutes | About 3.4 GB |

After the job finished, the Veeam console showed two restore points: the original full backup and the new incremental one. That showed me how Veeam builds a **backup chain** over time.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The Veeam Backups > Disk view listing both restore points for `DailyBackup_AllProduction`: the full backup from day one and the incremental from day two, with their sizes.

### Why I changed files first

If I had run the incremental backup straight after the first one without changing anything, Veeam would have had almost nothing to copy. The backup would have been tiny and I could not have appreciated how efficient incremental backups are.

This small experiment showed me that backup size depends directly on how much data changes between backups. In a real environment that matters for planning **storage requirements** and **backup windows** (the time period available for backups to run).

---

## 3.3 Verifying My Backups with SureBackup

Running a successful backup is reassuring. Knowing the backup can actually **boot and work** is better. That is what **SureBackup** does.

SureBackup starts a VM directly from its backup file inside an isolated test environment. Instead of assuming the backup is good, Veeam proves it.

### Steps I followed

1. In the Veeam console I went to **Home > Jobs > SureBackup**.
2. I created a new SureBackup job called `Verify_DailyBackup`.
3. I created a **Virtual Lab**, an isolated network where backup copies can start safely without affecting my production environment.
4. I added **DC01** as the application group because it was the most important server in my lab.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The SureBackup job `Verify_DailyBackup` in the Veeam console, with its Virtual Lab and DC01 listed as the application group.

### Verification tests I configured

| Test | Purpose | Expected result | Actual result | Status |
| ---- | ------- | --------------- | ------------- | ------ |
| Heartbeat test | Confirms the VM has powered on and VMware Tools is responding | Pass | Pass | ✅ |
| Ping test | Verifies the VM has basic network connectivity inside the isolated lab | Pass | Pass | ✅ |
| Application test | Checks important services are running. For my domain controller, Veeam verified that Active Directory was responding through LDAP | Pass | Pass | ✅ |
| CRC test | Confirms the backup files have not become corrupted while stored on disk | Pass | Pass | ✅ |

### What happened when I ran it

- Veeam booted DC01 directly from its backup without restoring it first.
- Once the operating system had fully started, Veeam ran every verification test automatically.
- A few minutes later every test had passed.
- The job reported that the **backup was valid**, the application tests had succeeded and the **protection group was verified**.
- Veeam then shut the VM down and cleaned up the isolated lab.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The completed SureBackup session with Heartbeat, Ping, Application and CRC tests all showing Passed and the result "Backup is valid".

> [!NOTE]
> My very first SureBackup run did not go smoothly. The Active Directory application test timed out because DC01 boots slowly on my 8 GB host. I explain the root cause and fix in [Challenges and Troubleshooting](08-challenges-and-troubleshooting.md#challenge-2-my-surebackup-verification-timed-out).

### Why SureBackup matters

| Question | Answer |
| -------- | ------ |
| **Why?** | A "successful" backup only means files were written. It does not prove the machine inside them will start. |
| **What does it accomplish?** | It proves the backup can boot, the operating system loads, and the services inside actually work. |
| **How does it work?** | Veeam starts the VM straight from the backup file inside the isolated Virtual Lab and runs automated tests. |
| **Production value** | You find out a backup is bad **before** a disaster, not during one. |
| **What if I skipped it?** | I could have "green" backups that fail the first time I need them. |

Before using SureBackup I assumed a successful backup meant I was protected. Afterwards I understood those are two very different things. The safest time to find out whether a backup works is before you ever need to restore it.

> [!IMPORTANT]
> SureBackup temporarily boots VMs from backup files, so it needs extra CPU, memory and storage. Because my lab has only 8 GB of RAM, I scheduled these jobs outside normal working hours so they would not slow the rest of the environment. In a larger production environment with more resources, they could run automatically alongside regular backups.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The SureBackup Virtual Lab console with DC01 running from its backup inside the isolated lab.

---

## Verification Summary

| Test | Expected result | Actual result | Status |
| ---- | --------------- | ------------- | ------ |
| First full backup completes for all three VMs | Success with a restore point for each VM | Success; about 35 minutes and 28 GB | ✅ |
| Incremental backup captures only changes | Much faster and smaller than the full backup | About 6 minutes and 3.4 GB | ✅ |
| Backup chain visible in the console | Full and incremental restore points listed | Both restore points listed | ✅ |
| SureBackup boots DC01 from backup | VM starts in the isolated lab | VM started and all tests ran | ✅ |
| SureBackup verification tests | All four tests pass | All four tests passed | ✅ |

---

## What I Took Away From This Section

- Backups must be run regularly and checked.
- Incremental backups save time and storage, but you only see that by measuring them.
- SureBackup proves backups actually work.
- Never assume. Always verify.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: A summary view of the backup results (full vs incremental duration and size) and the completed SureBackup job, to use as the section's overview graphic.

---

[← Previous: Installation and Configuration](02-veeam-installation-and-configuration.md) | [Next: Full and File-Level Restores →](04-full-and-file-level-restores.md)
