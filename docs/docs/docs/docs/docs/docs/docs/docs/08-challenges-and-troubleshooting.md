# 8. Challenges I Encountered and What They Taught Me

---

## Overview

No IT project goes perfectly from start to finish, and this one was no exception. Some of my biggest lessons came from things not working the first time.

Whenever something failed, I tried not to jump straight to an answer. I read the error messages, checked the logs, compared settings and worked out **why** it had happened before fixing it. That process taught me far more than a project where everything worked first time.

### Summary of challenges

| # | Problem | Root cause | Fix |
| - | ------- | ---------- | --- |
| 1 | First backup of DC01 failed during Application-Aware Processing | Veeam was using an ordinary domain user account, which does not have the privileges VSS needs on a Domain Controller | Changed the guest credentials to `LAB\Administrator` |
| 2 | SureBackup Active Directory test timed out | DC01 needed about 8 minutes to start Active Directory on my 8 GB host, but SureBackup only waited 5 minutes | Raised the boot timeout from 300 to 600 seconds |
| 3 | Restore to original location failed | DC01 was still registered in the VMware inventory from an earlier test | Removed DC01 from the VMware inventory, then restored again |

---

## Challenge 1: My First Backup Failed Because of VSS Permissions

### What happened

The first time I backed up DC01, I expected it to work because I had followed the setup carefully. Instead the backup failed. When I checked the Veeam job log, I saw the failure happened during **Application-Aware Processing**. The log mentioned a VSS writer error along with insufficient permissions for guest processing.

Key log lines from my notes:

```text
Application-aware processing failed
Error: VSS writer 'NTDS' failed
Error: Access is denied.
Failed to process guest
The guest interaction proxy can not be authenticated.
```

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The Veeam job session log for DC01 showing the failed Application-Aware Processing stage and the VSS writer / access denied error.

### Root cause analysis

After researching the error and reviewing my Veeam settings, I realised the problem was not Veeam at all. It was the credentials I had provided.

- I had configured Veeam to connect to the Domain Controller using an **ordinary domain user account**.
- Application-Aware Processing talks to Windows' **Volume Shadow Copy Service (VSS)** to create a consistent backup, and that requires administrative privileges.
- On a Domain Controller, only a Domain Administrator has the permissions needed.

### How I fixed it

1. I edited the backup job.
2. I replaced the guest credentials with my `LAB\Administrator` account.
3. I ran the backup again.

The logs confirmed Application-Aware Processing finished correctly:

```text
Application-aware processing started
VSS writers are quiesced successfully
Snapshot created successfully
Application-aware processing completed
Backup job completed successfully
```

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The backup job's Guest Processing credentials set to `LAB\Administrator`, and the next job log showing Application-Aware Processing completed successfully.

### What I learned

| Question | Answer |
| -------- | ------ |
| **Why does it matter?** | Choosing the correct credentials is as important as configuring the backup itself. |
| **What is the risk if Application-Aware Processing fails quietly?** | A backup that skips it may still complete, but it becomes **crash-consistent** instead of **application-consistent**. For services like Active Directory, that can decide whether a restore works properly. |
| **What do I do now?** | Whenever I configure backups for critical Windows servers, I check the guest processing logs to confirm VSS completed before I treat the backup as trustworthy. |

> [!WARNING]
> A green job status is not enough on its own. Read the guest processing section of the log.

---

## Challenge 2: My SureBackup Verification Timed Out

### What happened

Once my backups worked, I moved on to SureBackup. The first verification did not go as planned. The VM powered on and the **heartbeat test passed**, but after several minutes Veeam reported that the **Active Directory application test had timed out**.

Key log lines from my notes:

```text
Heartbeat test: PASSED
Running AD application test...
ERROR: Timeout waiting for application to start
SureBackup job failed
```

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The first SureBackup session with the heartbeat test passed and the Active Directory application test failed with a timeout.

### Root cause analysis

At first I thought my backup was broken. After investigating, I realised the backup was perfectly healthy.

- My lab runs on a computer with only 8 GB of RAM, so VMs start more slowly than on enterprise hardware.
- DC01 needed about **8 minutes** before Active Directory was fully operational.
- SureBackup was only waiting **5 minutes (300 seconds)** before declaring the test failed.

The backup was good. The testing environment was not configured for my hardware.

### How I fixed it

I increased the maximum boot timeout for the application group from **300 seconds to 600 seconds**. When I ran the verification again, SureBackup waited long enough for the Domain Controller to finish starting, and every test passed.

| Setting | Before | After |
| ------- | ------ | ----- |
| Application group startup / boot timeout | 300 seconds (5 minutes) | 600 seconds (10 minutes) |

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The SureBackup application group settings with the boot timeout set to 600 seconds, and the re-run session with all tests passed.

### What I learned

- A failed verification does not always mean the backup is bad. Sometimes the test environment is configured incorrectly.
- Timeout values should be matched to the real boot time of the systems being tested.
- Reading logs carefully can save a lot of unnecessary troubleshooting.

---

## Challenge 3: My Restore Failed Because the Virtual Machine Already Existed

### What happened

After restoring DC01 once, I repeated the exercise to become more confident. The second attempt produced another unexpected error. Partway through the restore, Veeam stopped and reported that the VM was **already registered in the VMware inventory**.

Key log lines from my notes:

```text
Registering VM in VMware...
ERROR: A VM with the same name is already registered.
Failed to register VM
Restore failed
```

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The failed Veeam restore session for DC01 with the "already registered" error message.

### Root cause analysis

At first I thought the backup files were damaged. They were not. The problem was my own doing:

- Between restore tests I had powered DC01 back on.
- The machine was running normally and was still registered inside VMware.
- When I told Veeam to restore to the **original location**, it tried to create another VM with the same identity, which caused a conflict.

### How I fixed it

1. I **removed DC01 from the VMware inventory** (I did not delete the VM, I just removed its registration).
2. Once VMware no longer had an existing registration for DC01, I started the restore again.
3. This time it completed successfully.

Another option would have been to restore the server to a completely different location under a new name.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: VMware with DC01 removed from the inventory, followed by the Veeam restore session completing successfully.

### What I learned

Successful disaster recovery is not only about knowing how to restore backups. It is also about understanding how the virtualisation platform behaves.

> [!IMPORTANT]
> Before restoring a machine to its **original location**, make sure VMware no longer has the old VM registered. Missing this small step could delay recovery during a real incident.

---

## Visual Summary

![Section 8 summary: challenges I encountered and what they taught me](../images/08-challenges-summary.png)

---

## Reflection

Although these issues slowed me down, I am glad they happened. If everything had worked perfectly the first time, I would have learned how to follow instructions but not how to troubleshoot.

Instead I gained experience reading logs, interpreting error messages, identifying root causes and making informed fixes. Those skills are just as valuable as knowing how to configure the software in the first place, and they are skills I will carry into any IT support or systems administration role.

---

[← Previous: Backup Integrity Test Record](07-backup-integrity-test-record.md) | [Next: Lessons Learned and Portfolio Summary →](09-lessons-learned-and-portfolio-summary.md)
