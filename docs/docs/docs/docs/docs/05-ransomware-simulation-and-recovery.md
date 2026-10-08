# 5. Ransomware Attack Simulation and Recovery

---

## Overview

This is the section I planned most carefully. Ransomware is one of the biggest cybersecurity threats organisations face today, and almost every week there is news of another company locked out of its systems.

I wanted to understand more than the theory. I wanted to see how ransomware affects data, what happens when built-in recovery options disappear, and how a properly configured backup system can bring everything back.

> [!IMPORTANT]
> **I did not use real ransomware.** I recreated its *behaviour* by manually simulating the damage it causes (renaming files and deleting Shadow Copies). This let me study the recovery process without putting my lab or my computer at risk.

One lesson stood out early. Modern ransomware is about much more than encrypting files. Many groups now **steal data before encrypting it**, then try to **destroy backups** so the victim has no choice but to pay. That changed how I viewed backups. It is not enough to have them; attackers must not be able to easily reach them.

---

## 5.1 Understanding How Ransomware Affects Data

Different ransomware families behave differently, but most follow a similar pattern. Typically ransomware will:

- spread across the infected computer,
- search every accessible drive and shared folder,
- encrypt documents, pictures, databases and other valuable files,
- rename those files with a new extension,
- delete **Windows Shadow Copies** to prevent easy recovery,
- try to locate and destroy backup systems,
- leave a ransom note demanding payment.

> **What are Shadow Copies?** They are point-in-time copies that Windows can keep of files so administrators can recover deleted or changed items. They are built into Windows, which is why attackers target them first.

For my simulation I focused on the two behaviours with the biggest impact on recovery:

1. **Encrypting shared files** (simulated by renaming them)
2. **Deleting Windows Shadow Copies**

These two actions let me test whether my backup strategy could still recover the data.

---

## 5.2 Preparing the Lab Before the Simulation

Before making any changes I created a clear **baseline** so I could compare the system before and after the attack. I documented the state of my file server:

- every shared folder
- how many files each folder contained
- several important files by name
- file sizes
- file creation dates

Then I ran a **fresh Veeam backup**. This created a clean restore point immediately before the simulated attack, and I recorded the exact backup timestamp because it would become my recovery point.

### File inventory (baseline)

The shares live under `CompanyShares$` on DC01.

| Shared folder | Files |
| ------------- | ----- |
| Finance | 12 |
| HR | 8 |
| IT | 9 |
| Projects | 7 |
| Marketing | 5 |
| Operations | 3 |
| Reports | 2 |
| Temp | 1 |
| **Total** | **47** |

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The baseline inventory of the DC01 file shares (folder names, file counts, sizes and creation dates) recorded before the simulation.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The Veeam job session for the fresh pre-attack backup with its completion time, which I used as my known-clean restore point.

### Why I created a backup immediately beforehand

| Question | Answer |
| -------- | ------ |
| **Why?** | Choosing the correct restore point is just as important as having backups. |
| **What does it accomplish?** | I knew exactly which restore point was clean, with a precise timestamp. |
| **Why does it matter in production?** | Ransomware can sit quietly on a system for days before it activates. Recent backups may already contain infected files, which is why keeping weekly and monthly backups matters. Sometimes the safest restore point is not yesterday's backup but one from several weeks ago. |
| **What if I skipped it?** | I could not have been certain which backup was clean. |

---

## 5.3 Simulating the Ransomware Attack

Since I was not using real malware, I needed another way to recreate what ransomware does. I used a simple PowerShell command that renamed every file in my shared folders by adding a fake `.LOCKED` extension. It does not encrypt anything, but it accurately simulates what users would see during an attack.

### Step 1: Simulating file encryption

I logged into **Client01** using a domain user account that had permission to access the shared folders, opened PowerShell, and ran the simulation:

```powershell
Get-ChildItem -Path "\\DC01\CompanyShares$\" -Recurse -File | Rename-Item -NewName { $_.FullName + '.LOCKED' }
```

Almost immediately every file in the shared folders was renamed with the `.LOCKED` extension. Watching hundreds of filenames change at once made the impact feel real. Even though I knew it was only a simulation, it was obvious how disruptive a ransomware attack would be for users trying to open their files.

> [!CAUTION]
> This command renames **every file** it can reach on the target share. Only run it in an isolated lab, against data you can afford to lose, and only with a tested backup in place.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: PowerShell on Client01 with the simulation command entered and run.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The `\\DC01\CompanyShares$` folder in File Explorer after the simulation, with the folders and files showing the `.LOCKED` extension.

### Step 2: Simulating Shadow Copy deletion

Modern ransomware often removes Shadow Copies because they are one of the easiest ways for administrators to recover deleted or modified files. To recreate that, I switched to **DC01**, opened an **elevated Command Prompt** and deleted every existing Shadow Copy:

```cmd
vssadmin delete shadows /all /quiet
```

The command reported that it had successfully deleted 3 Shadow Copies. Then I checked whether any remained:

```cmd
vssadmin list shadows
```

Windows returned:

```text
No items found that satisfy the query.
```

At that point the built-in Windows recovery option was gone. If I had not created proper backups beforehand, recovering those files would have been extremely difficult.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The elevated Command Prompt on DC01 running `vssadmin delete shadows /all /quiet`, followed by `vssadmin list shadows` returning "No items found that satisfy the query."

### Step 3: Testing backup isolation

Next I wanted to know whether my backup repository could be reached from another machine. If ransomware can encrypt your backups, they are no longer useful.

From **Client01**, I tried to browse to my backup server and access the backup repository.

| Test | Expected result | Actual result | Status |
| ---- | --------------- | ------------- | ------ |
| Access the backup repository from Client01 | Access denied or unreachable | I could not reach it. The repository was not shared over the network, and Windows Firewall stopped normal users from reaching it. Only Veeam could communicate with the backup storage. | ✅ |

> **📸 Screenshot Placeholder**
> Insert screenshot showing: Client01 failing to browse to the BackupServer's backup repository (for example, a network path error when trying to open the share).

#### What I learned

This was probably the biggest takeaway from the whole ransomware exercise. Until then, backup isolation felt like another security recommendation. After this test it made complete sense.

| Question | Answer |
| -------- | ------ |
| **Why does it matter?** | If I had stored my backups in a normal shared folder that everyone could access, my simulation could have reached them too. |
| **What protected me?** | Keeping the repository isolated and reachable only through Veeam protected the very thing that would save me. |
| **What if it were misconfigured?** | The backups could be renamed, encrypted or deleted along with the production data. |

---

## 5.4 Assessing the Damage

Before restoring anything I assessed the situation as an incident responder would. Instead of rushing into recovery, I first confirmed exactly what had been affected.

| Item | Result |
| ---- | ------ |
| Affected system | DC01 file server |
| Files affected | 47 files across 8 shared folders |
| Shadow Copies | Deleted |
| Veeam backups | Safe and available |
| Backup server | Unaffected |
| Clean restore point | Available from the previous night |
| Maximum possible data loss | Less than 24 hours |

Recovery decisions should be based on evidence, not assumptions. Writing everything down showed me how important it is to assess an incident before taking action.

---

## 5.5 Recovering from the Simulated Attack

Rather than restoring everything at once, I followed a structured process that mirrors how a real organisation would respond.

### Phase 1: Containing the incident

The first priority was to stop any further damage.

1. I disconnected DC01 from the network by **disabling its virtual network adapter** in VMware.
2. Before making any changes, I took a **VMware snapshot** of the affected machine.

My simulation could not spread like real ransomware, but I wanted to practise the same containment steps used in real incidents. Keeping a copy of the compromised system is good practice because it **preserves evidence** that could be used for forensic investigation.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: VMware showing DC01's network adapter disconnected, and the Snapshot Manager listing the snapshot of the affected DC01 taken before recovery.

### Phase 2: Choosing the right recovery method

I considered two options:

| Option | Assessment |
| ------ | ---------- |
| Restore the entire VM | Would fix the problem, but would also roll back every other legitimate change made since the backup |
| Restore only the affected files | Faster and affects only the damaged files |

The operating system, Active Directory and DNS were all still working normally. Only the shared files needed recovering, so I chose a **file-level restore**. This taught me that choosing the right method can significantly reduce downtime.

### Phase 3: Restoring the encrypted files

1. In Veeam, I opened the **File-Level Restore** browser and selected the clean restore point from the previous night.
2. I navigated to the `CompanyShares` folder and selected the entire folder.
3. I chose **Restore to Original Location** and let Veeam overwrite the renamed files with the clean versions from the backup.

The restore took about **eight minutes** and every affected file was recovered.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The Veeam File-Level Restore browser on the clean restore point (the previous night's backup) with the `CompanyShares` folder selected and the restore mode set to overwrite at the original location.

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The completed file restore session in Veeam with status Completed and a duration of about eight minutes.

### Phase 4: Verifying the recovery

I checked that:

- every folder contained the expected files,
- all filenames had their original extensions,
- documents opened normally,
- file sizes matched my original inventory,
- all 47 files had been restored.

Finally I reconnected DC01 to the network and confirmed that users could access the shared folders again.

| Check | Expected result | Actual result | Status |
| ----- | --------------- | ------------- | ------ |
| Folder contents | Expected files present in every folder | Present | ✅ |
| File extensions | Original names, no `.LOCKED` | Original extensions restored | ✅ |
| Documents open | Open normally | Opened normally | ✅ |
| File sizes | Match my inventory | Matched | ✅ |
| File count | 47 files restored | All 47 restored | ✅ |
| User access | Users can reach shared folders | Users could access shares normally | ✅ |

> **📸 Screenshot Placeholder**
> Insert screenshot showing: The `\\DC01\CompanyShares$` folders after recovery with normal names and no `.LOCKED` extension, and a document opened to confirm its contents.

### Recovery results

| Item | Result |
| ---- | ------ |
| Total recovery time | Approximately 34 minutes |
| Files restored | 47 |
| Data loss | None |
| Services interrupted | File shares only, during recovery |
| Active Directory | Continued operating normally |
| Recovery method | Veeam File-Level Restore |

---

## Why Deleting Shadow Copies Did Not Stop Me

Deleting the Windows Shadow Copies did not stop me from recovering the data. Veeam's backups are completely separate from Windows' built-in recovery features. Even with the Shadow Copies removed, my backups were safe because they sat in an isolated repository.

That is why organisations invest in dedicated backup solutions instead of relying solely on Windows recovery features.

> [!NOTE]
> My simulation was deliberately limited. Real ransomware may behave differently, for example by stealing data first or going after backup servers and credentials. My results show that this specific attack pattern was recoverable in my lab, not that every attack would be.

---

## Visual Summary

![Section 5 summary: ransomware attack simulation and recovery](../images/05-ransomware-summary.png)

---

## What I Took Away From This Section

- Backups are only useful if they can be restored.
- Backup isolation protects the safety net from the attack.
- File-level restore is faster and less disruptive than a full VM restore when the damage is limited to files.
- Regular restore testing builds confidence and exposes gaps before a real incident.

---

[← Previous: Full and File-Level Restores](04-full-and-file-level-restores.md) | [Next: Disaster Recovery Plan →](06-disaster-recovery-plan.md)
