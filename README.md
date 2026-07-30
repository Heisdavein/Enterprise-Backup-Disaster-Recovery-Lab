# Enterprise-Backup-Disaster-Recovery-Lab
Designed and implemented an enterprise-style Backup and Disaster Recovery environment using VMware Workstation and Veeam Backup &amp; Replication. The lab demonstrates VM-level backups, incremental backup strategies, SureBackup verification, ransomware recovery, file-level restores, full VM recovery, and disaster recovery planning.
#  Backup & Disaster Recovery Home Lab
### Building, Testing, Breaking and Recovering an Enterprise Backup Environment with Veeam

##  Overview

Backups are only valuable if they can be restored.

This project documents my hands-on implementation of an enterprise-style **Backup & Disaster Recovery (BDR)** environment using **Veeam Backup & Replication Community Edition** running in a VMware Workstation home lab.

Rather than simply configuring backup jobs, I intentionally created disaster scenarios—including complete server failure, accidental file deletion, and a simulated ransomware attack—to validate that my recovery strategy actually worked.

Throughout the project I measured recovery times, verified backup integrity, documented every recovery exercise, and developed a complete Disaster Recovery Plan.

---

##  Project Objectives

- Design an enterprise-style backup architecture
- Configure automated VM-level backups
- Implement incremental and synthetic full backups
- Configure backup copy jobs
- Verify backup integrity using SureBackup
- Perform full virtual machine recovery
- Perform file-level recovery
- Simulate ransomware recovery
- Build a documented Disaster Recovery Plan
- Measure Recovery Time Objectives (RTO) and Recovery Point Objectives (RPO)

---

# 🏗 Lab Environment

| Component | Technology |
|-----------|------------|
| Hypervisor | VMware Workstation Player |
| Backup Software | Veeam Backup & Replication Community Edition v12 |
| Host OS | Windows 11 |
| Backup Storage | 500GB External USB Drive |
| Domain Controller | Windows Server 2022 |
| Client | Windows 11 |
| Linux Server | Ubuntu Server 22.04 |
| Web Server | Apache |
| Network | VMware NAT |

---

#  Virtual Machines

| Machine | Purpose |
|----------|----------|
| DC01 | Active Directory, DNS & File Server |
| Client01 | Domain Joined Windows Client |
| WebServer01 | Ubuntu Apache Web Server |
| BackupServer | Veeam Backup & Replication |

---

#  Skills Demonstrated

- Backup Infrastructure Design
- Disaster Recovery Planning
- Veeam Backup & Replication
- VM-Level Image Backups
- Incremental Backups
- Synthetic Full Backups
- Backup Copy Jobs
- Grandfather-Father-Son (GFS) Retention
- Application-Aware Processing
- Windows Volume Shadow Copy Service (VSS)
- SureBackup Verification
- Full Virtual Machine Restore
- File-Level Restore
- Backup Repository Management
- Ransomware Recovery
- Backup Isolation
- Recovery Time Objective (RTO)
- Recovery Point Objective (RPO)
- Backup Integrity Testing
- VMware Administration
- PowerShell
- Windows Server Administration

---

#  Scenarios Tested

✅ Full Virtual Machine Restore

- Simulated complete Domain Controller failure
- Restored entire VM
- Verified Active Directory
- Verified DNS
- Verified domain authentication

---

✅ File-Level Recovery

- Deleted shared folder
- Emptied Recycle Bin
- Restored individual files directly from backup
- Zero downtime

---

✅ SureBackup Verification

- Boot verification
- Heartbeat test
- Ping test
- Active Directory verification
- CRC integrity checks

---

✅ Ransomware Recovery Simulation

To safely simulate ransomware behaviour I:

- Renamed files using PowerShell
- Simulated encrypted data
- Deleted Windows Shadow Copies
- Tested backup isolation
- Restored clean files using Veeam

No real malware was used during this project.

---

#  Results

| Test | Result |
|------|---------|
| Full VM Restore | ✅ Successful |
| File-Level Restore | ✅ Successful |
| SureBackup Verification | ✅ Successful |
| Backup Copy Job | ✅ Successful |
| Incremental Backups | ✅ Successful |
| Ransomware Recovery | ✅ Successful |

---

##  Recovery Metrics

| Metric | Result |
|---------|---------|
| Recovery Point Objective (RPO) | 24 Hours |
| Full VM Recovery | 22 Minutes |
| File-Level Recovery | 4–8 Minutes |
| Restore Success Rate | 100% |
| Data Loss | Zero |

---

#  Key Lessons Learned

One of the biggest lessons from this project was that **creating backups is only half the job**.

A backup should never be trusted simply because it completed successfully.

It should be:

- Tested
- Verified
- Restored
- Documented

I also gained practical experience troubleshooting VSS issues, configuring application-aware backups, validating backup integrity with SureBackup, and recovering from realistic disaster scenarios.

---

#  Repository Structure

```
Backup-Disaster-Recovery/
│
├── README.md
├── Documentation/
│     └── Backup & Disaster Recovery Laboratory Documentation.pdf
│
├
```

---
---

#  Technologies Used

- VMware Workstation
- Veeam Backup & Replication Community Edition
- Windows Server 2022
- Windows 11
- Ubuntu Server
- Active Directory
- DNS
- Apache
- PowerShell
- VSS

---

# 📖 Documentation

The complete project documentation covers:

- Environment Design
- Backup Architecture
- Backup Strategy
- Veeam Configuration
- Repository Design
- Backup Scheduling
- SureBackup
- Restore Testing
- Disaster Recovery Planning
- Backup Integrity Testing
- Ransomware Simulation
- Lessons Learned

---

#  Outcome

This project demonstrates practical experience in designing, implementing, testing, and validating a backup and disaster recovery solution using industry-standard tools.

Rather than simply configuring software, I intentionally created failure scenarios, recovered critical infrastructure, measured recovery performance, and documented the entire process to reflect real-world enterprise backup operations.

---

## 👤 Author

**Clinton Kehinde**

 IT Support | Systems Administrator | Cybersecurity Enthusiast
