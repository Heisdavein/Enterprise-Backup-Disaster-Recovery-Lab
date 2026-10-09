# 6. Developing My Disaster Recovery Plan

[← Previous: Ransomware Simulation and Recovery](05-ransomware-simulation-and-recovery.md) | [Next: Backup Integrity Test Record →](07-backup-integrity-test-record.md)

---

## Overview

After building my backup environment and testing several recovery scenarios, I realised that backups alone are not enough. If something serious happens, whether it is ransomware, accidental deletion, hardware failure or a server crash, you do not want to be working out what to do in the middle of the chaos. That is where a **Disaster Recovery (DR) plan** comes in.

A DR plan is a documented set of steps that explains exactly how to recover systems when something goes wrong. I wrote one for my lab because I wanted to practise responding to incidents the way an IT team would. Even in a home lab, treating it like production forced me to think about preparation, priorities, communication and recovery.

> [!TIP]
> The best time to create a disaster recovery plan is **before** you ever need one. When systems are down and users are waiting, nobody wants to rely on memory. A written plan lets whoever is responding work through the problem calmly and methodically.

---

## 6.1 Defining My Recovery Objectives

Before writing the procedures, I defined the goals my backup environment should achieve.

| Metric | What it means | Value for my lab |
| ------ | ------------- | ---------------- |
| **Recovery Point Objective (RPO)** | The maximum amount of data I am willing to lose if something goes wrong | 24 hours (backups run once a day) |
| **Recovery Time Objective (RTO)** | The maximum time I want a system to be unavailable | 2 hours for any production server |
| **Mean Time To Recover (MTTR)** | The average time my recovery tests actually took | 22 minutes for a full VM restore and 4 to 8 minutes for file-level restores |
| **Backup retention** | How long backups are kept before being replaced | 7 daily, 4 weekly and 3 monthly restore points |
| **Backup copy frequency** | How often backup copies are created | Weekly |
| **Backup verification** | How often backups are automatically tested | Weekly using SureBackup |

### Why these numbers come first

| Question | Answer |
| -------- | ------ |
| **Why?** | Backups are only one part of disaster recovery. The other part is deciding how much downtime and how much data loss is acceptable *before* an incident happens. |
| **How do they connect?** | RPO drives backup frequency (daily backups mean up to a day of lost data). RTO drives how fast recovery must be and what recovery method is needed. |
| **Why does it matter in production?** | Those two questions influence almost every backup decision that follows. |
| **What if I skipped it?** | I could not tell whether a recovery was fast enough or whether I was losing too much data. |

> [!NOTE]
> The RTO is a **target** (2 hours). The 22 minutes is what I **measured** in a real full-VM restore. My measured result comfortably beats my target.

---

## 6.2 Classifying Different Types of Incidents

Not every problem needs the same response. Losing a whole Domain Controller is far more serious than a user deleting one document, so I divided incidents into three priority levels.

| Severity | Description | Example | Response |
| -------- | ----------- | ------- | -------- |
| **P1: Critical** | Complete system failure or major data loss | Domain Controller unavailable, or a ransomware attack | Immediate response |
| **P2: Major** | Important service affected but the business can still operate | File server failure or a deleted shared folder | Restore within 1 hour |
| **P3: Minor** | Small issue affecting one user | Accidentally deleted file | Restore within 4 hours |

Creating these categories made me realise that disaster recovery is not just about restoring systems. It is also about deciding **which systems deserve attention first**.

---

## 6.3 My Disaster Recovery Procedure

I wrote the procedure as a checklist, so that someone else recovering my environment would not need to guess what to do next.

### Phase 1: Detect and assess the problem (target: 15 minutes)

The first job is to understand exactly what has happened.

- [ ] Receive the alert or user report.
- [ ] Record when the incident started.
- [ ] Open the Veeam console.
- [ ] Confirm backups are available.
- [ ] Check when the last successful backup was created.
- [ ] Identify which systems are affected.
- [ ] Decide whether the incident is P1, P2 or P3.
- [ ] If it is critical, notify the relevant people immediately.

Rushing into a restore without understanding the problem can make things worse.

### Phase 2: Contain the incident (target: 30 minutes for P1)

Before restoring anything, stop the problem from spreading.

- [ ] Disconnect affected virtual machines from the network.
- [ ] Take a VMware snapshot for evidence.
- [ ] Confirm the backup repository is still safe.
- [ ] Identify the last clean restore point.
- [ ] Estimate how much data might be lost.

During my ransomware simulation this phase was especially important. Stopping the attack before restoring prevents the restored files from being infected again straight away.

### Phase 3: Decide on the best recovery method

Choose the most appropriate recovery option.

| Situation | Recovery method |
| --------- | --------------- |
| Hardware failure | Full virtual machine restore |
| Deleted files | File-level restore |
| Ransomware affecting only shared files | Restore only the affected files instead of rebuilding the entire server |
| Ransomware where the operating system or Active Directory is also compromised | Full restore |

> [!TIP]
> The fastest recovery is not always restoring everything. Restoring only what is damaged can be quicker and far less disruptive.

### Phase 4: Restore the data (target: within the RTO)

- [ ] Start the restore in Veeam.
- [ ] Monitor the progress until it finishes.
- [ ] Record the start and finish times.
- [ ] Note any errors encountered.
- [ ] Keep the restored server disconnected from the network until I have confirmed everything works.

### Phase 5: Verify everything

This is probably the most important stage. A restore finishing successfully does not automatically mean everything works. Before putting the server back into production I would verify that:

- [ ] Windows starts correctly.
- [ ] Active Directory is working.
- [ ] DNS resolves correctly.
- [ ] Shared folders are accessible.
- [ ] Important files are present.
- [ ] Client computers can authenticate successfully.

If any check failed, I would keep troubleshooting rather than reconnect the server.

### Phase 6: Return the system to service

- [ ] Reconnect the server to the network.
- [ ] Monitor it for around 30 minutes.
- [ ] Watch for unexpected errors.
- [ ] Inform users that services have been restored.
- [ ] Record the total recovery time.

This lets me compare the actual recovery against the RTO I defined earlier.

### Phase 7: Review the incident (within 48 hours)

After everything is back to normal I would ask:

- What caused the incident?
- What went well during recovery?
- What could have been improved?
- Do my backup schedules need changing?
- Should I improve my security controls?

Every incident, even a simulated one, is a chance to improve the recovery process.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The written DR procedure checklist (phases 1 to 7) as saved in my documentation.

---

## 6.4 Deciding Which Systems Are Most Important

Not every system needs to be restored at the same time. I ranked each VM by how important it would be to the organisation.

| System | Priority | Reason |
| ------ | -------- | ------ |
| **DC01** (Domain Controller) | Priority 1 | Everything depends on Active Directory and DNS. Without it, users cannot log in. |
| **File Server** (DC01) | Priority 1 | Stores important business files and shared folders. |
| **WebServer01** | Priority 2 | Important for hosting web services but not as critical as the Domain Controller. |
| **Client01** | Priority 3 | A single user workstation with the lowest business impact. |
| **BackupServer** | Priority 0 | The backup server must remain protected because every recovery depends on it. |

Disaster recovery is driven by **business impact**, not by which machine is easiest to restore.

---

## 6.5 My Backup Schedule

I summarised the schedule in one place so anyone managing the environment can see when backups happen and when they are verified.

| Task | Schedule |
| ---- | -------- |
| Daily incremental backup | Every night at 23:00 |
| Synthetic full backup | Every Sunday at 23:00 |
| Backup copy job | Every Sunday after the full backup |
| SureBackup verification | Every Sunday after the backup copy |
| Backup review | First Monday of every month |

---

## 6.6 How This Would Be Different in a Real Organisation

My lab shows the core principles of enterprise backup, not every enterprise feature. In production I would expect:

| Area | My lab | A real organisation |
| ---- | ------ | ------------------- |
| Off-site copy | Another folder on the same drive | Backups replicated to cloud storage, another office or a dedicated DR site |
| Ransomware-proof backups | Isolated repository only | Immutable or air-gapped backups that ransomware cannot modify or delete |
| Monitoring | I check the Veeam console myself | Automatic monitoring with alerts when a backup fails |
| Backup locations | One external drive | Multiple locations for resilience |
| Encryption | Not enabled | Backup files encrypted in case the storage device is lost or stolen |

---

## Verification

| Check | Expected result | Actual result | Status |
| ----- | --------------- | ------------- | ------ |
| Recovery objectives defined | RPO, RTO and MTTR documented with real values | Documented in section 6.1 | ✅ |
| Measured recovery vs RTO target | Recovery faster than the 2 hour RTO | 22 minutes for a full VM restore | ✅ |
| Incident severity levels defined | P1, P2 and P3 with response times | Documented in section 6.2 | ✅ |
| Recovery procedure written | Checklist anyone could follow | 7-phase checklist in section 6.3 | ✅ |
| System priorities ranked | Every VM given a recovery priority | Documented in section 6.4 | ✅ |
| Schedule documented | Backup, copy, verification and review times | Documented in section 6.5 | ✅ |

---

## Reflection

Writing this plan brought together everything I had learned. I stopped thinking only about configuring backup software and started thinking about how an organisation actually recovers from an outage: prioritising systems, documenting procedures, measuring recovery times and planning for business continuity.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: A summary view of the DR plan (RPO/RTO table, severity levels and system priority list) to use as the section's overview graphic.

---

[← Previous: Ransomware Simulation and Recovery](05-ransomware-simulation-and-recovery.md) | [Next: Backup Integrity Test Record →](07-backup-integrity-test-record.md)
