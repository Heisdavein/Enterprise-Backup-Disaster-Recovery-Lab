# 9. Lessons Learned and Portfolio Summary

---

## Overview

When I started, my understanding of backup and disaster recovery was mostly theoretical. I knew why backups mattered, but I had never built a backup solution from scratch, tested different recovery scenarios or measured recovery times.

By the end I had **planned, built, tested, broken, restored and documented** an entire backup and disaster recovery environment. I did not just configure the technology. I proved it worked through repeated recovery testing.

---

## What I Built and Tested

- Deployed Veeam Backup & Replication on a dedicated backup server that was isolated from the production virtual machines.
- Configured a backup repository on an external USB hard drive so backup data was physically separated from the systems it protected.
- Created a daily backup strategy using incremental backups, weekly synthetic full backups and a seven-day retention policy.
- Enabled Application-Aware Processing using Windows VSS to produce application-consistent backups for Active Directory.
- Configured a Grandfather-Father-Son (GFS) backup copy job with weekly and monthly retention.
- Built and tested a SureBackup job that automatically verified that backups could boot and that Active Directory services were working.
- Restored an entire Domain Controller after a simulated hardware failure, in 22 minutes with zero data loss.
- Performed a file-level restore to recover a deleted folder of twelve documents in about four minutes.
- Simulated a ransomware attack by renaming shared files and deleting Windows Shadow Copies.
- Recovered all 47 affected files with Veeam's file-level restore, without losing any data.
- Designed and documented a Disaster Recovery plan with recovery objectives, incident severity levels, a recovery procedure, system priorities and a backup schedule.
- Kept a backup testing log of every recovery exercise.

---

## Results

| Metric | Result |
| ------ | ------ |
| Recovery Point Objective (RPO) | 24 hours |
| Recovery Time Objective (RTO) target | 2 hours for any production server |
| Measured full VM recovery time | 22 minutes |
| File-level recovery time | 4 to 8 minutes |
| Recovery and integrity tests completed | 6 |
| Test success rate | 100% |
| Data loss during testing | Zero |
| Ransomware recovery | Successful |
| Backup isolation | Verified |

These figures were not estimates. They were measured during recovery tests that I carried out myself.

![Section 9 summary: portfolio summary](../images/09-portfolio-summary.png)

---

## Lessons Learned

1. **A backup is only valuable if it can be restored.** A green tick means files were written. Only a restore, or a SureBackup boot test, proves they are usable.
2. **Backups should be judged by whether they can be restored, not by whether they complete.**
3. **Application-Aware Processing needs the right credentials.** Without administrator-level credentials my Domain Controller backup silently failed the VSS step, and a crash-consistent backup of Active Directory is not something to rely on.
4. **A failed test is not always a failed backup.** My SureBackup timeout was a configuration problem, not a backup problem.
5. **Choose the right recovery method.** A file-level restore recovered my deleted folder in 4 minutes with no downtime, where a full restore would have taken over 20 minutes and rolled back other changes.
6. **Isolation protects the safety net.** My backup repository was not reachable from the machine that ran the simulated attack.
7. **Know how your virtualisation platform behaves.** Restoring to the original location fails if the old VM is still registered in VMware.
8. **Write the plan before the disaster.** A written DR plan removes guesswork when everyone is under pressure.
9. **Measure and record everything.** Real recovery times turned RTO from a textbook term into a number I could defend.
10. **Older restore points matter.** Ransomware can sit quietly for days, so weekly and monthly (GFS) restore points are not optional extras.

---

## Security Considerations

| Control | How it is implemented in my lab | Why it matters |
| ------- | ------------------------------- | -------------- |
| Dedicated backup server | BackupServer is a separate VM, not the domain controller | A compromise of a protected server does not automatically compromise the backup system |
| Separate physical storage | Repository on the external HDD, not the SSD | One drive failure or encryption event cannot destroy both data and backups |
| Repository isolation | Not shared over the network; Windows Firewall blocks normal users | Ransomware on another machine cannot reach the backups (tested from Client01) |
| Privileged guest credentials | `LAB\Administrator` used for Application-Aware Processing | Needed for a consistent Active Directory backup (see [Challenge 1](08-challenges-and-troubleshooting.md#challenge-1-my-first-backup-failed-because-of-vss-permissions)) |
| GFS retention | 4 weekly and 3 monthly restore points | Gives clean restore points if an attack goes unnoticed for weeks |
| Evidence preservation | VMware snapshot of the affected VM before recovery | Preserves the compromised state for later investigation |
| Audit trail | Restore reason entered for every restore | Shows who restored what and why |

> [!WARNING]
> I did not enable backup encryption in this lab. Production backups, especially those stored off-site or in the cloud, should be encrypted at rest.

---

## Production Considerations

A real environment would go further than my lab:

- **Off-site copies:** replicate backups to cloud storage, another office or a dedicated DR site instead of another folder on the same drive.
- **Immutable or air-gapped backups:** backups that ransomware cannot modify or delete.
- **Automated monitoring and alerting:** alerts when a backup fails, instead of manually checking the console.
- **Multiple backup locations:** more than one location for extra resilience.
- **Encryption:** encrypted backup files in case storage is lost or stolen.
- **vCenter Server:** enterprise VMware environments connect Veeam to vCenter rather than directly to a single host.
- **Dedicated SQL Server:** larger environments normally use a full SQL Server for Veeam's configuration database.
- **More resources for verification:** SureBackup can run alongside normal operations when there is enough CPU, memory and storage.

---

## Best Practices I Applied

- Keep backup infrastructure separate from the systems it protects.
- Never store the only backup on the same physical storage as the original data.
- Use application-aware backups for critical workloads such as Active Directory.
- Use GFS retention and backup copy jobs.
- Verify backups with SureBackup and test restores regularly.
- Record the reason for every restore.
- Define RPO and RTO before an incident, then measure against them.
- Document recovery procedures and rank systems by business impact.

---

## Future Improvements

- Send the backup copy to a genuinely separate location, such as cloud storage or a second site.
- Add immutable or air-gapped backup storage.
- Enable backup encryption.
- Add automated monitoring and alerting for failed backups.
- Protect the BackupServer's own configuration database separately.
- Store backups in more than one location.

---

## Technologies and Tools I Used

- Veeam Backup & Replication v12 Community Edition
- VMware Workstation Player
- Windows Server 2022 (Backup Server and Domain Controller)
- Windows 11 (domain-joined client workstation)
- Ubuntu Server 22.04 (Apache web server)
- Windows Volume Shadow Copy Service (VSS)
- PowerShell and Command Prompt for the ransomware simulation

---

## Skills This Project Demonstrates

| Skill area | Evidence in this repository |
| ---------- | --------------------------- |
| Backup design | Dedicated backup server, separate repository, 3-2-1 thinking ([doc 1](01-lab-environment-and-architecture.md)) |
| Veeam administration | Installation, repository, jobs, copy job and GFS ([doc 2](02-veeam-installation-and-configuration.md)) |
| Backup verification | Incremental testing and SureBackup ([doc 3](03-running-and-verifying-backups.md)) |
| Recovery | Full VM and file-level restores with measured times ([doc 4](04-full-and-file-level-restores.md)) |
| Incident response | Ransomware simulation, containment, assessment and recovery ([doc 5](05-ransomware-simulation-and-recovery.md)) |
| Disaster recovery planning | RPO/RTO, severity levels, 7-phase procedure ([doc 6](06-disaster-recovery-plan.md)) |
| Audit and documentation | Integrity test log ([doc 7](07-backup-integrity-test-record.md)) |
| Troubleshooting | Three root cause analyses ([doc 8](08-challenges-and-troubleshooting.md)) |

---

## Conclusion

This project demonstrates that I understand backup and disaster recovery as a complete process rather than a piece of software. I learned to install and configure Veeam, design backup jobs, choose retention policies, protect Active Directory with application-aware backups, verify backup integrity, recover both entire VMs and individual files, and respond to a simulated ransomware incident.

The biggest lesson I took away is that backups should never be judged by whether they complete successfully. They should be judged by whether they can be restored successfully. Every major backup I created was tested, verified and documented.

Looking back, this project was not simply about learning Veeam. It was about developing the mindset of an IT professional who understands that protecting systems is only half the job. The real responsibility is making sure those systems can be recovered quickly, safely and with minimal disruption when something goes wrong.

> *A backup you have never tested is not a backup. It is hope.*

---

[← Previous: Challenges and Troubleshooting](08-challenges-and-troubleshooting.md) | [Back to README](../README.md)
