# 7. Backup Integrity Testing Record

---

## Overview

Throughout this project I made it a priority not to configure backups and then assume they worked. Every time I completed a backup or recovery exercise, I recorded the result so I could track what had been tested and how the system performed.

This record shows that my backup strategy was not based on assumptions. It was tested under different scenarios and consistently produced successful recoveries.

In a real organisation, records like this become part of the **backup audit trail**. They show that backup systems are not only configured correctly but also tested regularly, which many security standards and compliance frameworks expect.

---

## Backup Integrity Test Log

| # | Test performed | Scenario | Result | Recovery time |
| - | -------------- | -------- | ------ | ------------- |
| 1 | Full VM restore: DC01 | Simulated hardware failure | ✅ Pass | 22 minutes |
| 2 | File-level restore | Accidental folder deletion | ✅ Pass | 4 minutes |
| 3 | File-level restore | Simulated ransomware attack | ✅ Pass | 8 minutes |
| 4 | SureBackup verification: DC01 | Automated backup integrity verification | ✅ Pass | 12 minutes |
| 5 | Incremental backup integrity | CRC checksum verification | ✅ Pass | N/A |
| 6 | Backup copy job | Secondary backup copy completed successfully | ✅ Pass | N/A |

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The completed test log (all six tests marked Pass) as recorded in my documentation.

---

## Detailed Verification

Each test proved something different.

| # | Purpose | Test performed | Expected result | Actual result | Outcome |
| - | ------- | -------------- | --------------- | ------------- | ------- |
| 1 | Prove I can recover an entire server after complete hardware failure | Deleted DC01 and its virtual disk, then ran Veeam Entire VM restore to the original location | DC01 restored with IP, name, Active Directory, DNS and desktop files intact; domain logins work | All items verified. Client01 authenticated against the restored DC. | ✅ |
| 2 | Prove I can recover deleted files without disrupting the server | Deleted the 12-document `Financial_Q3` folder, emptied the Recycle Bin, then used Guest Files (Windows) restore | All 12 documents back with no server downtime | All 12 documents restored; no services stopped | ✅ |
| 3 | Prove I can recover from a simulated ransomware attack | Renamed files with `.LOCKED`, deleted Shadow Copies, then restored `CompanyShares` from the clean restore point | All 47 files returned to original names and contents | All 47 files restored; sizes and extensions matched my inventory | ✅ |
| 4 | Prove my backups actually boot and work | SureBackup job `Verify_DailyBackup` booted DC01 in the isolated lab and ran heartbeat, ping, application (LDAP) and CRC tests | Backup is valid and the protection group is verified | All four tests passed | ✅ |
| 5 | Prove the stored backup files are not corrupted | CRC checksum verification of the incremental backup | No corruption found | Passed | ✅ |
| 6 | Prove a second copy of my backups exists | Checked that the backup copy job completed to `E:\VeeamBackups\OffCopy` | Copy completes successfully | Completed successfully | ✅ |

> **📸 Screenshot Placeholder**
> Insert screenshot showing: Evidence for test 1: DC01 running after the full restore, with the IP address and Active Directory working.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: Evidence for tests 2 and 3: the restored `Financial_Q3` folder (12 files) and the restored `CompanyShares` folders (47 files) after recovery.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: Evidence for test 4: the SureBackup job `Verify_DailyBackup` completed with Heartbeat, Ping, Application and CRC tests passed.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: Evidence for test 6: the Veeam backup copy job session showing Success and the secondary copy in `E:\VeeamBackups\OffCopy`.

> [!NOTE]
> The six tests are a mix of three **restore** tests, one **automated boot verification**, one **file-integrity check** and one **backup copy check**. I count them together as my backup testing record, but I am careful not to call all six "restores".

---

## Results

| Metric | Result |
| ------ | ------ |
| Tests completed | 6 |
| Tests passed | 6 |
| Success rate | 100% |
| Data loss during testing | Zero |
| Ransomware recovery | Successful |
| Backup isolation | Verified |

---

## What This Record Taught Me

Every test completed successfully, and each one showed something different about the reliability of my strategy:

- The **full VM restore** confirmed I could recover an entire server after a complete hardware failure.
- The **file-level restores** showed I could recover individual files quickly without affecting the rest of the server.
- The **SureBackup verification** proved my backups were not just stored. They were bootable and functional.
- The **backup copy job** confirmed I had a secondary copy, supporting the principles of the 3-2-1 backup strategy (with the limitations described in [document 1](01-lab-environment-and-architecture.md#the-3-2-1-backup-rule)).

> [!IMPORTANT]
> A backup that has never been restored is something you *hope* will work. A backup that has been restored, verified and documented is one you can trust.

---

[← Previous: Disaster Recovery Plan](06-disaster-recovery-plan.md) | [Next: Challenges and Troubleshooting →](08-challenges-and-troubleshooting.md)
