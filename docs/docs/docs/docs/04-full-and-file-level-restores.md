# 4. Performing Full and File-Level Restorations

## Overview

Creating backups is only half the story. The real test is whether they can bring a system back when something goes wrong. Anyone can say they have backups, but until you have restored something successfully, you do not know whether they will save you.

I wanted to experience recovery while I was calm and had time to learn, not during a stressful incident where every minute counts. I tested two scenarios:

1. **A complete server failure:** I deleted an entire VM and restored it from backup.
2. **Accidental file deletion:** one of the most common problems IT support deals with, solved with a file-level restore.

---

## 4.1 Simulating a Complete Server Failure

### Preparing the failure

Before deleting anything, I made sure I could verify the restore afterwards. I documented the current state of my domain controller, **DC01**:

- its IP address
- its computer name
- the Active Directory configuration
- the DNS records
- several test files I saved on the desktop

I also noted the exact backup restore point I planned to use.

Then I:

1. Shut down DC01 inside VMware.
2. Deliberately deleted the VM, **including its virtual hard disk**.

At that point the server no longer existed on my computer. The only remaining copy was inside my Veeam backup repository. That is exactly the situation I wanted to simulate: a complete hardware failure where the original server is gone.

> [!CAUTION]
> I only deleted DC01 *after* confirming a good restore point existed and noting which one I would use. Never delete the only copy of a machine in a lab, or anywhere else, until you know your backup is usable.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The VMware Workstation Player library with DC01 no longer listed (Client01, WebServer01 and BackupServer still present), confirming the VM was deleted.

### Restoring the virtual machine

In the Veeam console I went to **Home > Backups > Disk**, then:

1. Right-clicked the `DailyBackup_AllProduction` backup job.
2. Selected **Restore > Entire VM**.
3. Chose **DC01** from the list of backed-up VMs.
4. Selected the restore point from the previous day.
5. Chose **Restore to original location**, which tells Veeam to recreate the VM exactly where it originally existed.
6. When Veeam asked for a restore reason, I entered:

```text
   Testing full VM recovery from simulated hardware failure.
```

7. Reviewed the summary and clicked **Finish**.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The Full VM Restore wizard's Restore Mode page with "Restore to original location" selected.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The Full VM Restore wizard's Reason page with the text "Testing full VM recovery from simulated hardware failure." entered.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The Full VM Restore summary page showing DC01, the chosen restore point and the restore mode before clicking Finish.

#### Why I filled in the restore reason

| Question | Answer |
| -------- | ------ |
| **Why?** | Even in a home lab I made it a habit to complete the reason field. |
| **What does it accomplish?** | It creates a record of why a restore was performed. |
| **Why does it matter in production?** | Restore operations are usually audited. A recorded reason supports incident investigations and gives useful documentation if anyone reviews what happened later. |
| **What if I skipped it?** | There would be no audit trail explaining who restored what, and why. |

Building good habits in a lab makes them natural in a production environment.

### Watching the restore

| Stage | What happened |
| ----- | ------------- |
| Extracting VM configuration | Veeam read the hardware configuration stored inside the backup. |
| Registering the VM | VMware recreated the VM using the original settings. |
| Restoring the virtual disk | Veeam copied the backed-up data back onto the storage. |
| Completing the restore | The VM started automatically and reconnected to the network. |

The entire restore finished in about **22 minutes**.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The Veeam restore session for DC01 showing each stage completed and the total duration of about 22 minutes.

### Verification

When DC01 powered on, the first thing I did was check that everything had been restored correctly.

| Item verified | Expected result | Actual result | Status |
| ------------- | --------------- | ------------- | ------ |
| IP address | Same as before deletion | Correct | ✅ |
| Computer name | Same as before deletion | Correct | ✅ |
| Active Directory | Working normally | Working normally | ✅ |
| DNS records | All records present | All records were present | ✅ |
| Desktop test files | Present | Restored successfully | ✅ |
| Domain authentication | Client01 can authenticate | Client01 authenticated successfully against the restored domain controller | ✅ |

Everything worked exactly as it did before the server was deleted. That proved my backup was not just sitting on a hard drive. It could recover an entire production server.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: DC01 after the restore, with `ipconfig /all` output showing the original IP address and computer name, plus Active Directory Users and Computers open and working.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: Client01 logged in with a domain account after the restore, proving authentication against the restored DC01.

### My first real Recovery Time Objective (RTO)

From the moment I started the restore until the server was fully operational took roughly **22 minutes**. That gave me something I had only read about before: a real **Recovery Time Objective (RTO)** measurement.

> **What is an RTO?** It is the amount of time it takes to recover a system after it fails (and, as a target, the longest a business can tolerate a system being down).

Every organisation has a limit on how long important systems can be unavailable. If a business expects its domain controller back online within an hour, restoring it in 22 minutes comfortably meets that. Instead of guessing, I now had **real data from my own environment**.

> [!NOTE]
> On my second restore attempt of DC01, I hit an error because the VM was still registered in the VMware inventory. I explain the cause and fix in [Challenges and Troubleshooting](08-challenges-and-troubleshooting.md#challenge-3-my-restore-failed-because-the-virtual-machine-already-existed).

---

## 4.2 Performing a File-Level Restore

Restoring a whole server was interesting, but most IT support requests are much simpler. More often than not, a user has accidentally deleted files and needs them back. That is where Veeam's **File-Level Restore** is useful. Instead of restoring the entire VM, I can recover only the files or folders that are needed.

### Creating the scenario

1. Inside a shared folder on DC01, I created a folder called `Financial_Q3` containing **twelve documents**.
2. Once I confirmed everything was there, I deleted the entire folder.
3. To make recovery more realistic, I also **emptied the Recycle Bin**.

At that point there was no normal Windows way of getting the files back. The only remaining copy was inside my backup.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The shared folder on DC01 with the `Financial_Q3` folder containing twelve documents, before deletion.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The same shared folder after deletion with `Financial_Q3` missing and an empty Recycle Bin.

### Restoring the deleted files

In the Veeam console I selected **Restore > Guest Files (Windows)**, then:

1. Selected **DC01**.
2. Chose the backup created *before* the files were deleted.
3. Let Veeam mount the backup.

A new window opened and Veeam displayed the backup almost like another hard drive. I could browse folders exactly as they existed when the backup was taken. There was no need to restore the entire server or even restart the VM.

4. I navigated to the shared folder and found `Financial_Q3` where I expected it, with all twelve documents inside.
5. I right-clicked the folder and selected **Restore to original location**.
6. Within moments Veeam copied the folder back to DC01.
7. I checked the shared folder and confirmed everything had been restored.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The Veeam File-Level Restore browser with the DC01 backup mounted, the `Financial_Q3` folder selected, and the right-click menu showing "Restore to original location".

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The shared folder on DC01 after the restore, with `Financial_Q3` back and all twelve documents present.

### Results

| Recovery item | Result |
| ------------- | ------ |
| Recovery time | Approximately 4 minutes |
| Files restored | 12 documents |
| Server downtime | None |
| Restore method | Direct file recovery from backup |

The server kept running the whole time. No users were disconnected and no services stopped. The only thing that changed was that the missing folder reappeared.

### Full restore vs file-level restore

| Question | Answer |
| -------- | ------ |
| **Why use file-level restore here?** | I only needed one folder back. |
| **What would a full restore have cost?** | I would have taken the domain controller offline for over twenty minutes and rolled back every other change made since the backup. |
| **What did I do instead?** | Recovered only what I needed in about four minutes without interrupting any services. |
| **Why does it matter in production?** | Choosing the right recovery method is as important as having good backups. Sometimes a full server restore is correct. Other times a single folder is all that is needed. |

---

## Test Summary

| Test | Expected result | Actual result | Time | Status |
| ---- | --------------- | ------------- | ---- | ------ |
| Full VM restore of DC01 after deletion | DC01 recovered with identity, AD and DNS intact | Recovered; AD, DNS, IP, name and domain logins all verified | About 22 minutes | ✅ |
| File-level restore of deleted `Financial_Q3` folder | All 12 documents recovered with no server downtime | All 12 documents recovered; no downtime | About 4 minutes | ✅ |

---

## Visual Summary

![Section 4 summary: full and file-level restores](../images/04-restores-summary.png)

---

## What I Took Away From This Section

- Running backups is not enough. I have to verify restores.
- A full VM restore recovers entire systems and produces a real RTO figure.
- File-level restores are fast, non-disruptive and extremely useful day to day.
- Documenting restore procedures and testing them turns a plan that works on paper into a recovery that works in reality.

---

[← Previous: Running and Verifying Backups](03-running-and-verifying-backups.md) | [Next: Ransomware Simulation and Recovery →](05-ransomware-simulation-and-recovery.md)
