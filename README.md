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

Before I Started: Why I Chose Backup and Disaster Recovery
Before I began this project, I spent some time thinking about why backup and disaster recovery mattered so much. The more I researched, the more I realized that backups are often one of the most overlooked parts of IT. They are usually configured once, left running in the background, and forgotten about until something goes wrong. Unfortunately, that's often when organizations discover that their backups have been failing silently for weeks or even months.
I didn't want to develop that mindset. More importantly, I didn't want to say I understood backup and disaster recovery simply because I had watched tutorials or read about them. I wanted practical experience that would give me confidence in my own abilities and prepare me for real-world IT environments.
One lesson quickly stood out to me throughout this project: a backup is only valuable if it can actually be restored. Simply creating backups isn't enough. They have to be tested regularly to ensure they work when they're needed most. That idea became the driving force behind this entire lab.
Rather than stopping after configuring backup jobs, I wanted to go a step further by deliberately creating failure scenarios and proving that I could recover from them successfully. Throughout this project, I simulated situations that administrators might genuinely face, including accidental file deletion, hardware failure, and even a ransomware attack. After each scenario, I restored the affected systems, measured how long recovery took, and documented everything I learned along the way.
For this lab, I chose Veeam Backup & Replication Community Edition because it is one of the most widely used backup solutions in enterprise environments. The Community Edition provides many of the same core features as the commercial version while remaining free for personal labs, making it an excellent platform for learning. Since many organizations rely on Veeam to protect their virtual infrastructure, the skills I developed during this project closely reflect what I would encounter in a production environment.
I also intentionally included a ransomware recovery scenario because ransomware has become one of the most significant cybersecurity threats facing organisations today. Modern attacks don't just encrypt files they can disrupt business operations, cause significant financial loss, and even target backup systems themselves. By understanding how backup solutions help organizations recover from ransomware, and by learning how to configure backups in a way that makes them more resilient, I gained practical knowledge that is valuable for both IT Support and cybersecurity roles.
By the time I completed this project, I had a much deeper appreciation for the importance of backup testing and disaster recovery planning. More than anything, I learned that backups are not just about copying data—they're about ensuring that an organization can continue operating when the unexpected happens. That lesson is one I'll carry into every IT environment I work in.

Installing and Configuring Veeam Backup & Replication
With my lab environment fully set up, the next step was to install the software that would manage all of my backups. For this project, I used Veeam Backup & Replication Community Edition, one of the most widely used backup solutions in enterprise environments.

Although installing Veeam is fairly straightforward, I quickly realised that the real work begins after the installation. The decisions I made during the initial setup such as where to store backups, how long to keep them, and when backups should run would determine how reliable my backup system would be later on.

Instead of rushing through the installation wizard, I took time to understand each option and why it mattered. That way, I wasn't just clicking Next repeatedly I was building a backup solution that reflected the way many organisations protect their virtual infrastructure.

2.1 Downloading and Installing Veeam Community Edition

To begin, I downloaded the free Community Edition directly from Veeam's website.

1.     I visited the Veeam website and navigated to Products > Veeam Backup & Replication.

2.     I selected the Community Edition, which required creating a free Veeam account.

3.     After downloading the installation ISO (around 4 GB), I mounted it inside my BackupServer virtual machine.

4.     I launched the installer by running setup.exe.

5.     During installation, I accepted the default components, which included:

o   Veeam Backup Server

o   Backup Catalog

o   Veeam Console

Since this was a standalone backup server for my home lab, I didn't install Enterprise Manager, as I wouldn't be using it.

I also accepted the default installation location:

C:\Program Files\Veeam

For the configuration database, I chose SQL Server Express, which Veeam installs automatically.

The installation took about fifteen minutes to complete. Once it finished, I restarted the BackupServer VM before opening Veeam for the first time.

Why I made this decision

I chose SQL Server Express because my lab only contained a handful of virtual machines.

Veeam uses this database to store important information such as backup jobs, restore points, schedules, and configuration settings. For a small lab like mine, SQL Express provides more than enough performance without the extra complexity of installing and managing a full SQL Server.

In larger enterprise environments that protect hundreds or thousands of virtual machines, administrators would normally use a dedicated SQL Server instead.

2.2 Adding My VMware Environment

Once Veeam was installed, it still didn't know anything about my virtual machines.

Before it could protect them, I first had to tell Veeam where those machines were running.

To do this:

1.     I opened the Veeam Backup & Replication Console.

2.     From the left-hand menu, I selected Backup Infrastructure, then Managed Servers.

3.     I right-clicked inside the window and selected Add Server.

Normally, organisations running VMware use vCenter Server, which allows Veeam to manage many ESXi hosts from one place.

Since my lab was built using VMware Workstation, I couldn't use vCenter. Instead, I added my VMware host using administrator credentials.

After connecting successfully, Veeam automatically discovered all of the virtual machines in my lab:

·        DC01

·        Client01

·        WebServer01

·        BackupServer

Seeing all of my virtual machines appear inside Veeam was an important milestone because it confirmed that the backup server could now communicate with the environment it was going to protect.

What I learned

Although my setup is much smaller than a production environment, the overall process is very similar.

The biggest difference is that enterprise environments usually connect Veeam to vCenter Server, while my home lab connects directly to the VMware host.

Even so, the core backup concepts remain exactly the same.

2.3 Configuring the Backup Repository

The next thing I needed to configure was the backup repository.

The repository is simply the location where Veeam stores every backup it creates. Without it, there would be nowhere to save my backup files.

To configure it, I:

1.     Opened Backup Infrastructure and selected Backup Repositories.

2.     Chose Add Backup Repository.

3.     Selected Direct Attached Storage, since my backup drive was physically connected to the BackupServer.

4.     Named the repository HomeLabBackupRepo.

5.     Set the storage location to:

E:\VeeamBackups

This folder was located on my external 500 GB hard drive.

One setting I paid close attention to was Maximum Concurrent Tasks.

I reduced this value to 2, meaning Veeam could only process two backup jobs at the same time.

Why I made this decision

My host computer only has 8 GB of RAM, and several virtual machines were already running.

If Veeam tried backing up every VM simultaneously, it would place unnecessary pressure on the CPU, memory, and storage, making the entire lab slower and less stable.

By limiting the number of concurrent backup tasks, I sacrificed a little backup speed in exchange for a much more reliable system.

I also enabled Decompress backup data blocks before storing.

Although this uses a little more storage space, it can make restores faster, which is exactly what matters during a disaster recovery situation.

2.4 Creating My Backup Job

With the repository ready, I could finally create my first backup job.

A backup job tells Veeam:

·        what to back up,

·        where to store it,

·        how often to run,

·        and how many backups to keep.

For this project, I created one backup job that protected my three production virtual machines:

·        DC01

·        Client01

·        WebServer01

I intentionally left BackupServer out of this job because the backup server manages the backups itself. Its own configuration database can be protected separately.

After selecting my virtual machines, I configured the repository and chose to keep seven restore points, giving me one week's worth of daily backups.

For the backup method, I selected:

·        Daily Incremental Backups

·        Synthetic Full Backup every Sunday

This means that during the week, Veeam only backs up the data that has changed since the previous backup.

Then every Sunday, it automatically creates a new full backup without having to read all the data from the virtual machines again.

This approach saves both storage space and backup time.

One feature I made sure to enable was Application-Aware Processing.

I also enabled Guest File System Indexing and entered the administrator credentials for each virtual machine.

Why this was one of the most important settings

Out of everything I configured in Veeam, Application-Aware Processing was probably the setting I considered most important.

Without it, Veeam simply captures the virtual machine exactly as it is at that moment.

Imagine pulling the power cable out of a computer while someone is saving a file.

The computer shuts down instantly, and although most data survives, there's always a chance something wasn't finished writing to disk.

That's similar to what happens with a basic crash-consistent backup.

Application-Aware Processing works differently.

Before taking the snapshot, Windows tells running applications to finish writing their data properly.

Only then does Veeam create the backup.

For important services like Active Directory, databases, or Microsoft Exchange, this makes a huge difference because it helps ensure the backup can be restored without hidden corruption.

2.5 Creating a Backup Copy Job

Creating backups is only part of a good backup strategy.

If those backups are lost, corrupted, or encrypted by ransomware, I could still end up with nothing.

To reduce that risk, I created a Backup Copy Job.

Instead of backing up the virtual machines again, this job copies the backups that Veeam has already created and stores them in another location.

For my lab, I used:

E:\VeeamBackups\OffCopy

Although this was still on my external hard drive, it allowed me to simulate an off-site backup location and better understand how backup copies work.

I configured the copy job to run every Sunday night, after the weekly synthetic full backup had finished.

I also enabled GFS (Grandfather-Father-Son) Retention, keeping:

·        Weekly backups for four weeks.

·        Monthly backups for three months.

Why I chose GFS retention

At first, keeping seven daily backups seemed like enough.

But then I thought about a real-world scenario.

What if ransomware infected a server and nobody noticed for two weeks?

By the time the attack was discovered, all seven daily backups could already contain the encrypted data.

In that situation, rolling backups alone wouldn't help.

GFS retention solves this problem by keeping older weekly and monthly backup copies.

That means even if something goes unnoticed for a long time, there's still a much better chance of finding a clean backup from several weeks—or even months earlier.

Working through this section helped me understand that backup isn't just about creating copies of data. It's about planning for the unexpected and making sure I always have a reliable recovery point, even when problems aren't discovered immediately.
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/88bba673-5b94-4721-b6df-f723b7921609" />

Section 3  Running Backups and Verifying Their Integrity
Creating a backup job is only the beginning. Running it is the next step. The part that really matters, though, is proving that the backup actually works.
While researching backup and disaster recovery, I came across several real-world incidents where organisations believed their backups were working, only to discover during an emergency that they had been failing silently for weeks or even months. By the time they realised, it was already too late.
That completely changed the way I thought about backups.
I didn't want to simply create a backup job, see a green tick, and assume everything was fine. I wanted evidence that my backups could actually restore a system when it mattered. So after creating my backup jobs, I focused on testing them, understanding what Veeam was doing behind the scenes, and verifying that every restore point was usable.
________________________________________
3.1  Running My First Backup
With everything configured, it was finally time to run my first backup.
1.	Inside the Veeam Backup & Replication console, I located the DailyBackup_AllProduction job.
2.	I right-clicked the job and selected Start.
The backup started almost immediately.
At the bottom of the Veeam console, I could watch the progress in real time as each virtual machine moved through different stages of the backup process. Since I had limited my job to two concurrent tasks, Veeam processed two virtual machines at a time before moving on to the third.
As I watched the job run, I noticed that every VM followed the same sequence of steps.
Stage	What I learned
Creating Snapshot	Veeam asks VMware to create a snapshot of the virtual machine, capturing the exact state of the disk at that moment.
Processing VSS	Windows Volume Shadow Copy Service (VSS) prepares running applications so the backup is consistent.
Transferring Data	Veeam copies the VM data from the snapshot into the backup repository.
Committing Snapshot	Once the data has been copied, VMware removes the snapshot and merges any new changes back into the original virtual disk.
Indexing Guest Files	Veeam indexes the files inside the operating system, making it easier to restore individual files later.
Completing	The backup finishes and a new restore point is created.
The very first backup was a full backup, meaning Veeam copied every bit of data from each protected virtual machine.
The entire process took about 35 minutes and used approximately 28 GB of storage.
When the job finished, all three virtual machines showed a green Success status along with their first restore point.
What I Learned
Watching the backup run helped me understand something that had confused me before.
I used to think a virtual machine somehow paused while it was being backed up, but that isn't what happens at all.
Instead, VMware creates a snapshot of the virtual machine. While Veeam reads data from that snapshot, the VM continues running normally. Any new changes are temporarily written to a separate delta file. Once the backup is complete, VMware merges those changes back into the main virtual disk.
In other words, the virtual machine keeps running the entire time. From the user's perspective, nothing changes—they can continue working without even knowing a backup is taking place.
Understanding that process made me appreciate how modern backup software protects live systems without causing downtime.
________________________________________
3.2 Running an Incremental Backup
The following day, I wanted to see how an incremental backup behaved.
Unlike a full backup, an incremental backup only saves the data that has changed since the previous backup. This makes backups much faster and requires significantly less storage.
Before running the job again, I deliberately made changes to each virtual machine.
•	On DC01, I created several new files.
•	On WebServer01, I modified a few configuration files.
•	On Client01, I updated some user account settings.
I wanted to create enough changes for the backup to capture something meaningful.
Once I had finished making those changes, I ran the same backup job again.
This time, the difference was obvious.
Instead of taking around 35 minutes, the backup completed in roughly 6 minutes.
Instead of consuming 28 GB, it only needed about 3.4 GB of storage.
After the job completed, I checked the Veeam console and could clearly see two restore points:
•	the original full backup
•	the new incremental backup
Seeing both restore points helped me understand how Veeam builds a backup chain over time.
Why I Did This
I deliberately changed files before running the incremental backup because I wanted to see a noticeable difference.
If I had run the backup immediately after the first one without changing anything, Veeam would have had almost nothing new to copy. The backup would have been tiny, making it difficult to appreciate just how efficient incremental backups really are.
This small experiment showed me how backup size depends directly on how much data changes between backups—an important concept when planning storage requirements and backup windows in a real production environment.
________________________________________
3.3  Verifying My Backups with SureBackup
Running a successful backup is reassuring.
Knowing that the backup can actually boot and function correctly is even better.
This is where one of Veeam's most impressive features comes in: SureBackup.
SureBackup automatically starts a virtual machine directly from its backup file inside an isolated testing environment. Instead of assuming the backup is good, Veeam actually proves it.
When I first learned about this feature, it completely changed the way I thought about backup verification.
To set it up, I completed the following steps:
1.	In the Veeam console, I navigated to Home > Jobs > SureBackup.
2.	I created a new SureBackup job called Verify_DailyBackup.
3.	I created a Virtual Lab, which is an isolated network where backup copies can safely start without affecting my production environment.
4.	I added DC01 as the application group because it was the most important server in my lab.
Next, I configured the verification tests that Veeam would perform automatically.
Verification Test	Purpose
Heartbeat Test	Confirms that the virtual machine has powered on successfully and VMware Tools is responding.
Ping Test	Verifies that the virtual machine has basic network connectivity inside the isolated lab.
Application Test	Checks that important services are running. For my domain controller, Veeam verified that Active Directory was responding through LDAP.
CRC Test	Confirms that the backup files themselves have not become corrupted while stored on disk.
After configuring everything, I started the SureBackup job.
Watching the process was fascinating.
Veeam booted DC01 directly from its backup without restoring it first.
Once the operating system had fully started, Veeam automatically performed every verification test.
A few minutes later, every test passed successfully.
The job reported that the backup was valid, the application tests had succeeded, and the protection group had been fully verified.
After the tests finished, Veeam automatically shut down the virtual machine and cleaned up the isolated lab environment.
What I Learned
SureBackup was easily one of the most valuable features I explored during this project.
Before using it, I assumed that a successful backup meant I was protected.
After using it, I realised those are two very different things.
A backup isn't truly trustworthy until you've proven that it can boot, that the operating system loads correctly, and that the services inside it actually work.
SureBackup does exactly that.
Rather than waiting for a disaster to discover whether my backups were usable, I had already tested them in advance.
For me, that was one of the biggest lessons from this project: the safest time to find out whether a backup works is before you ever need to restore it.
Note: SureBackup temporarily boots virtual machines from their backup files, so it requires additional CPU, memory, and storage resources. Because my lab machine only has 8 GB of RAM, I scheduled these verification jobs outside normal working hours to avoid slowing down the rest of the environment. In a larger production environment with more resources, these tests could run automatically alongside regular backup operations.
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/f71bfdac-216b-423c-8c98-172924fd1903" />

Section 4  Performing Full and File-Level Restorations
Creating backups is only half of the story. The real test is whether those backups can actually bring a system back when something goes wrong.
That was the part I was most interested in.
Anyone can say they have backups, but until you've restored something successfully, you don't really know if they're going to save you when disaster strikes.
I wanted to experience the recovery process while I was calm and had time to learn, not during a stressful situation where every minute mattered.
To do that, I tested two different recovery scenarios.
First, I simulated a complete server failure by deleting an entire virtual machine and restoring it from backup.
Then, I simulated one of the most common problems IT support teams deal with every day—someone accidentally deleting important files.
These two scenarios gave me practical experience with the types of restores I would most likely perform in a real organisation.
________________________________________
4.1  Simulating a Complete Server Failure
Preparing the Failure
Before deleting anything, I wanted to make sure I could properly verify the restore afterwards.
To do that, I documented the current state of my domain controller (DC01).
I recorded:
•	its IP address
•	its computer name
•	the Active Directory configuration
•	the DNS records
•	several test files I had saved on the desktop
I also noted the exact backup restore point I planned to use.
Once everything was documented, I shut down DC01 inside VMware.
Then I deliberately deleted the virtual machine, including its virtual hard disk.
At that moment, the server no longer existed on my computer.
The only remaining copy of it was inside my Veeam backup repository.
That was exactly the situation I wanted to simulate—a complete hardware failure where the original server is gone.
Although deleting a working server felt slightly uncomfortable, it gave me confidence that the recovery test would be realistic.
________________________________________
Restoring the Virtual Machine
With the server gone, it was time to bring it back.
Inside the Veeam Backup & Replication console, I navigated to:
Home → Backups → Disk
From there, I:
1.	Right-clicked the DailyBackup_AllProduction backup job.
2.	Selected Restore, then Entire VM.
3.	Chose DC01 from the list of backed-up virtual machines.
4.	Selected the restore point from the previous day.
5.	Chose Restore to original location, which tells Veeam to recreate the virtual machine exactly where it originally existed.
Before starting the restore, Veeam asked for a reason.
I entered:
Testing full VM recovery from simulated hardware failure.
Why I Did This
Even though this was only a home lab, I made it a habit to complete the reason field.
In real organisations, restore operations are usually audited.
Recording why a restore was performed helps create an audit trail, supports incident investigations, and provides useful documentation if anyone needs to review what happened later.
Building good habits in a lab makes them feel natural in a production environment.
After reviewing everything, I clicked Finish, and the recovery process began.
________________________________________
Watching the Restore Process
As Veeam worked, I watched each stage complete one after another.
Stage	What happened
Extracting VM Configuration	Veeam read the hardware configuration stored inside the backup.
Registering the VM	VMware recreated the virtual machine using the original settings.
Restoring the Virtual Disk	Veeam copied the backed-up data back onto the storage.
Completing the Restore	The virtual machine started automatically and reconnected to the network.
The entire restore finished in about 22 minutes.
When DC01 powered on, the first thing I did was check whether everything had been restored correctly.
I verified:
Item	Result
IP Address	Correct
Computer Name	Correct
Active Directory	Working normally
DNS Records	All records were present
Desktop Test Files	Restored successfully
Domain Authentication	Client01 authenticated successfully against the restored domain controller
Everything worked exactly as it had before the server was deleted.
That was an incredibly satisfying moment because it proved that my backup wasn't just sitting on a hard drive—it could actually recover an entire production server.
What I Learned
One thing that stood out to me was the recovery time.
From the moment I started the restore until the server was fully operational again took roughly 22 minutes.
That gave me something I had only ever read about before—a real Recovery Time Objective (RTO).
The RTO is simply the amount of time it takes to recover a system after it fails.
Knowing this number is valuable because every organisation has a limit on how long important systems can be unavailable.
If a business expects its domain controller to be back online within an hour, then restoring it in 22 minutes comfortably meets that requirement.
Instead of guessing how long recovery would take, I now had real data from my own environment.
________________________________________
4.2  Performing a File-Level Restore
While restoring an entire server was interesting, I also knew that most IT support requests are much simpler.
More often than not, users accidentally delete files and need them restored.
That's where Veeam's File-Level Restore feature becomes incredibly useful.
Rather than restoring the entire virtual machine, Veeam allows me to recover only the files or folders that are needed.
________________________________________
Creating the Scenario
To test this feature properly, I created a realistic situation.
Inside one of the shared folders on DC01, I created a folder called Financial_Q3 containing twelve documents.
Once I confirmed everything was there, I deleted the entire folder.
To make the recovery more realistic, I also emptied the Recycle Bin.
At that point, there was no normal Windows method of getting those files back.
The only remaining copy existed inside my backup.
________________________________________
Restoring the Deleted Files
Inside the Veeam console, I selected:
Restore → Guest Files (Windows)
I then:
1.	Selected DC01.
2.	Chose the backup created before the files were deleted.
3.	Allowed Veeam to mount the backup.
Within a few moments, a new window opened.
What impressed me most was that Veeam displayed the backup almost like another hard drive.
I could browse through folders exactly as they existed when the backup was taken.
There was no need to restore the entire server or even restart the virtual machine.
I simply navigated to the shared folder and found the Financial_Q3 folder sitting exactly where I expected it.
All twelve documents were still there.
I right-clicked the folder and selected Restore to original location.
Within moments, Veeam copied the folder back to DC01.
After checking the shared folder, I confirmed that everything had been restored successfully.
________________________________________
Results
Recovery Item	  Result
Recovery Time	Approximately 4 minutes
Files Restored	12 documents
Server Downtime	None
Restore Method	Direct file recovery from backup
The server continued running throughout the entire process.
No users were disconnected.
No services stopped.
The only thing that changed was that the missing folder reappeared.
________________________________________
What I Learned
This exercise showed me why file-level restores are so valuable in day-to-day IT support.
If I had restored the entire virtual machine just to recover one deleted folder, I would have taken my domain controller offline for over twenty minutes and rolled back every other change made since the backup.
Instead, Veeam allowed me to recover only what I needed in about four minutes, without interrupting any services.
It was a great reminder that choosing the right recovery method is just as important as having good backups.
Sometimes recovering an entire server is the right choice.
Other times, restoring a single folder is all that's needed.
Learning when to use each approach is an important part of becoming confident with backup and disaster recovery.
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/b3a9051f-7388-46ca-8e46-f3bf54f584d9" />

Section 5. Ransomware Attack Simulation and Recovery
This was probably the section I planned the most carefully because ransomware is one of the biggest cybersecurity threats organisations face today. Almost every week, there's news of another company losing access to its systems because of a ransomware attack.
I wanted to understand more than just the theory. I wanted to see how ransomware affects data, what happens when recovery options disappear, and most importantly, how a properly configured backup system can bring everything back.
For safety reasons, I did not use real ransomware.
Instead, I recreated its behaviour by manually simulating the damage it causes. That allowed me to study the recovery process without putting my lab or my computer at risk.
One important lesson I learned early on is that modern ransomware is about much more than encrypting files. Many ransomware groups now steal data before encrypting it, then try to destroy backups so victims have no choice but to pay.
That completely changed how I viewed backups.
It's not enough to simply have backups—you also have to make sure attackers can't easily reach them.

5.1  Understanding How Ransomware Affects Data
Before I simulated the attack, I wanted to understand exactly what ransomware normally does after infecting a computer.
Although different ransomware families behave differently, most follow a similar pattern.
Typically, ransomware will:
•	spread across the infected computer
•	search every accessible drive and shared folder
•	encrypt documents, pictures, databases, and other valuable files
•	rename those files with a new extension
•	delete Windows Shadow Copies to prevent easy recovery
•	try to locate and destroy backup systems
•	leave behind a ransom note demanding payment
For my simulation, I focused on two behaviours that have the biggest impact on recovery:
•	encrypting shared files
•	deleting Windows Shadow Copies
These two actions would allow me to test whether my backup strategy could still recover the data.

5.2  Preparing the Lab Before the Simulation
Before making any changes, I wanted to create a clear baseline so I could compare the system before and after the attack.
I started by documenting the current state of my file server.
I recorded:
•	every shared folder
•	how many files each folder contained
•	several important files by name
•	file sizes
•	file creation dates
Once I had finished documenting everything, I ran a fresh Veeam backup.
This created a clean restore point immediately before the simulated attack.
I also recorded the exact backup timestamp because I knew that would become my recovery point later.
Why I Did This
One thing I learned while researching ransomware is that choosing the correct restore point is just as important as having backups.
If ransomware has been sitting quietly on a system for days before activating, recent backups may already contain the infected files.
Creating a backup immediately before my simulation meant I knew exactly which restore point was clean.
It also helped me understand why keeping weekly and monthly backups is so important. Sometimes the safest restore point isn't yesterday's backup it might be one from several weeks ago.

5.3  Simulating the Ransomware Attack
Since I wasn't using real malware, I needed another way to recreate what ransomware normally does.
I wrote a simple PowerShell script that renamed every file inside my shared folders by adding a fake .LOCKED extension.
This didn't encrypt the files, but it accurately simulated what users would see during a ransomware attack.
Step 1  Simulating File Encryption
I logged into Client01 using a domain user account that had permission to access the shared folders.
From PowerShell, I ran my simulation script.
Almost immediately, every file inside the shared folders was renamed with the .LOCKED extension.
Watching hundreds of filenames suddenly change made the impact feel surprisingly real.
Even though I knew it was only a simulation, it became obvious how disruptive a ransomware attack would be for users trying to access their files.

Step 2  Simulating Shadow Copy Deletion
Modern ransomware often removes Windows Shadow Copies because they're one of the easiest ways for administrators to recover deleted or modified files.
To recreate that behaviour, I switched to DC01 and opened an elevated Command Prompt.
I then deleted every existing Shadow Copy.
Afterwards, I checked whether any remained.
Windows returned:
No items found that satisfy the query.
At that point, the built-in Windows recovery option was gone.
If I hadn't created proper backups beforehand, recovering those files would have been extremely difficult.

Step 3  Testing Backup Isolation
The next thing I wanted to test was whether my backup repository could be reached from another machine.
If ransomware can encrypt your backups, then your backups are no longer useful.
From Client01, I attempted to browse to my backup server and access the backup repository.
I wasn't able to.
The repository wasn't shared over the network, and Windows Firewall prevented normal users from reaching it.
Only Veeam itself could communicate with the backup storage.
What I Learned
This was probably the biggest takeaway from the entire ransomware exercise.
Until then, backup isolation had felt like another security recommendation.
After this test, it made complete sense.
If I had simply stored my backups inside a normal shared folder that everyone could access, my simulation could have reached them as well.
By keeping the backup repository isolated and accessible only through Veeam, I had protected the very thing that would save me during an attack.

5.4  Assessing the Damage
Before restoring anything, I wanted to evaluate the situation just as an incident responder would.
Instead of rushing straight into recovery, I first confirmed exactly what had been affected.
Item	Result
Affected system	DC01 file server
Files affected	47 files across 8 shared folders
Shadow Copies	Deleted
Veeam backups	Safe and available
Backup server	Unaffected
Clean restore point	Available from the previous night
Maximum possible data loss	Less than 24 hours
Seeing everything written down helped me understand how important it is to assess an incident before taking action.
Recovery decisions should always be based on evidence rather than assumptions.

5.5  Recovering from the Simulated Attack
Rather than restoring everything immediately, I followed a structured recovery process.
This mirrors how a real organisation would respond after discovering ransomware.

Phase 1 Containing the Incident
The first priority was preventing any further damage.
I disconnected DC01 from the network by disabling its virtual network adapter inside VMware.
Although my simulation couldn't actually spread like real ransomware, I wanted to practise the same containment process used during real incidents.
Before making any changes, I also created a VMware snapshot of the infected machine.
Keeping a copy of the compromised system is considered good practice because it preserves evidence that could later be used for forensic investigation.

Phase 2 Choosing the Right Recovery Method
Next, I had to decide how I wanted to recover the data.
I considered two options.
The first was restoring the entire virtual machine.
The second was restoring only the affected files.
Since the operating system, Active Directory, and DNS were all still working normally, only the shared files needed recovering.
For that reason, I chose a file-level restore.
Why I Chose This Approach
Restoring the entire virtual machine would certainly have fixed the problem.
However, it would also have rolled back every other legitimate change made since the backup was taken.
A file-level restore was much faster and affected only the damaged files.
This exercise taught me that choosing the right recovery method can significantly reduce downtime.

Phase 3  Restoring the Encrypted Files
Inside Veeam, I opened the File-Level Restore browser and selected the clean restore point from the previous night.
I navigated to the CompanyShares folder and selected the entire folder for recovery.
I chose Restore to Original Location and allowed Veeam to overwrite the renamed files with the clean versions stored in the backup.
The restore completed in about eight minutes.
Every affected file was successfully recovered.

Phase 4  Verifying the Recovery
With the restore complete, I carefully checked that everything had returned to normal.
I confirmed that:
•	every folder contained the expected files
•	all filenames had their original extensions
•	documents opened normally
•	file sizes matched my original inventory
•	all 47 files had been restored successfully
Finally, I reconnected DC01 to the network and confirmed that users could once again access the shared folders without any issues.

Recovery Results
Item	Result
Total recovery time	            Approximately 34 minutes
Files restored	            47
Data loss	            None
Services interrupted	            File shares only during recovery
Active Directory	            Continued operating normally
Recovery method	            Veeam File-Level Restore

What I Learned
This exercise reinforced one of the biggest lessons from the entire project.
Deleting Windows Shadow Copies didn't stop me from recovering the data.
That's because Veeam's backups are completely separate from Windows' built-in recovery features.
Even though the Shadow Copies had been removed, my backups were still safe because they were stored in an isolated backup repository.
For me, that clearly demonstrated why organisations invest in dedicated backup solutions instead of relying solely on Windows recovery features.
More importantly, it taught me that a good backup strategy isn't just about creating backups—it's about making sure those backups remain available even when everything else has gone wrong.
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/01f8fae6-71ab-48c5-ae8c-68921cce9994" />

Section 6  Developing My Disaster Recovery Plan
After building my backup environment and successfully testing different recovery scenarios, I realised something important.
Having backups alone isn't enough.
If a serious incident happens—whether it's ransomware, accidental deletion, hardware failure, or a server crash—you don't want to be figuring out what to do in the middle of the chaos. That's where a Disaster Recovery (DR) plan becomes essential.
A disaster recovery plan is simply a documented set of steps that explains exactly how to recover systems when something goes wrong.
I decided to create one for my lab because I wanted to practise responding to incidents the same way an IT team would in a real organisation. Even though this was only a home lab, treating it like a production environment helped me think beyond simply taking backups. It forced me to think about preparation, priorities, communication, and recovery.
One lesson became very clear while working on this section:
The best time to create a disaster recovery plan is before you ever need one.
When systems are down and users are waiting, nobody wants to rely on memory. A written plan removes the guesswork and allows whoever is responding to work through the problem calmly and methodically.

6.1  Defining My Recovery Objectives
Before writing the recovery procedures, I first defined the goals I wanted my backup environment to achieve.
Metric	What it Means	Value for My Lab
Recovery Point Objective (RPO)	The maximum amount of data I'm willing to lose if something goes wrong.	24 hours (because backups run once every day)
Recovery Time Objective (RTO)	The maximum amount of time I want a system to be unavailable.	2 hours for any production server
Mean Time To Recover (MTTR)	The average time my recovery tests actually took.	22 minutes for a full VM restore and 4–8 minutes for file-level restores
Backup Retention	How long backups are kept before being replaced.	7 daily, 4 weekly and 3 monthly restore points
Backup Copy Frequency	How often backup copies are created.	Weekly
Backup Verification	How often backups are automatically tested.	Weekly using SureBackup
Working through these numbers helped me understand that backups are only one part of disaster recovery.
The other part is deciding how much downtime is acceptable and how much data loss is acceptable before an incident ever happens.
Those two questions influence almost every backup decision that follows.

6.2  Classifying Different Types of Incidents
Not every problem requires the same response.
Losing an entire Domain Controller is obviously much more serious than a user accidentally deleting a document, so I divided incidents into three priority levels.
Severity	Description	Example	Response
P1 – Critical	Complete system failure or major data loss	Domain Controller unavailable or ransomware attack	Immediate response
P2 – Major	Important service affected but business can still operate	File server failure or deleted shared folder	Restore within 1 hour
P3 – Minor	Small issue affecting one user	Accidentally deleted file	Restore within 4 hours
Creating these categories made me realise that disaster recovery isn't just about restoring systems—it's also about deciding which systems deserve attention first.

6.3 My Disaster Recovery Procedure
To make the recovery process easy to follow, I wrote it as a checklist.
If someone else had to recover my environment, they wouldn't need to guess what to do next—they could simply work through each stage.

Phase 1 Detect and Assess the Problem
The first thing I would do is understand exactly what has happened.
My checklist is:
•	Receive the alert or user report.
•	Record when the incident started.
•	Open the Veeam console.
•	Confirm backups are available.
•	Check when the last successful backup was created.
•	Identify which systems are affected.
•	Decide whether the incident is P1, P2 or P3.
•	If it's a critical incident, notify the relevant people immediately.
I learned that rushing into a restore without first understanding the problem can sometimes make things worse.

Phase 2  Contain the Incident
Before restoring anything, I would stop the problem from spreading.
Depending on the incident, I would:
•	Disconnect affected virtual machines from the network.
•	Take a VMware snapshot for evidence.
•	Confirm the backup repository is still safe.
•	Identify the last clean restore point.
•	Estimate how much data might be lost.
During my ransomware simulation, this step became especially important.
Stopping the attack before restoring data prevents the restored files from immediately becoming infected again.

Phase 3  Decide on the Best Recovery Method
Next, I would choose the most appropriate recovery option.
For example:
•	Hardware failure → Perform a full virtual machine restore.
•	Deleted files → Perform a file-level restore.
•	Ransomware affecting only shared files → Restore only the affected files instead of rebuilding the entire server.
One of the biggest lessons I learned during this project was that the fastest recovery isn't always restoring everything.
Sometimes restoring only what's damaged is quicker and causes far less disruption.

Phase 4  Restore the Data
Once I know what needs restoring, I can begin the recovery.
During this stage I would:
•	Start the restore in Veeam.
•	Monitor the progress until it finishes.
•	Record the start and finish times.
•	Note any errors encountered.
•	Keep the restored server disconnected from the network until I've confirmed everything is working properly.

Phase 5  Verify Everything
This is probably the most important stage.
Just because a restore finishes successfully doesn't automatically mean everything is working.
Before putting the server back into production, I would verify that:
•	Windows starts correctly.
•	Active Directory is working.
•	DNS resolves correctly.
•	Shared folders are accessible.
•	Important files are present.
•	Client computers can authenticate successfully.
If any of these checks failed, I would continue troubleshooting rather than reconnecting the server.
Working through this process taught me that verification is just as important as the restore itself.

Phase 6  Return the System to Service
Once I was confident everything was functioning normally, I would:
•	Reconnect the server to the network.
•	Monitor it for around 30 minutes.
•	Watch for unexpected errors.
•	Inform users that services have been restored.
•	Record the total recovery time.
This allows me to compare the actual recovery against the RTO I defined earlier.

Phase 7  Review the Incident
After everything is back to normal, I would review what happened.
I would ask questions like:
•	What caused the incident?
•	What went well during recovery?
•	What could have been improved?
•	Do my backup schedules need changing?
•	Should I improve my security controls?
I found this stage valuable because every incident—even a simulated one—is an opportunity to improve the recovery process.

6.4  Deciding Which Systems Are Most Important
Not every system needs to be restored at the same time.
I ranked each virtual machine based on how important it would be to the organisation.
System	Priority	Reason
DC01 (Domain Controller)	Priority 1	Everything depends on Active Directory and DNS. Without it, users can't log in.
File Server	Priority 1	Stores important business files and shared folders.
WebServer01	Priority 2	Important for hosting web services but not as critical as the Domain Controller.
Client01	Priority 3	A single user workstation with the lowest business impact.
BackupServer	Priority 0	The backup server must remain protected because every recovery depends on it.
Creating this priority list helped me understand that disaster recovery is driven by business impact, not simply by which machine is easiest to restore.

6.5  My Backup Schedule
To make my disaster recovery plan easy to understand, I summarised the backup schedule in one place.
Task	Schedule
Daily incremental backup	Every night at 23:00
Synthetic full backup	Every Sunday at 23:00
Backup Copy Job	Every Sunday after the full backup
SureBackup verification	Every Sunday after the backup copy
Backup review	First Monday of every month
Having this schedule documented means anyone managing the environment can immediately understand when backups occur and when they are verified.

6.6  How This Would Be Different in a Real Organization
Although this project taught me a great deal about backup and disaster recovery, I also recognised that a real production environment would include additional layers of protection.
For example:
•	Instead of storing backup copies in another folder on the same drive, organisations usually replicate backups to cloud storage, another office, or a dedicated disaster recovery site.
•	Many companies use immutable or air-gapped backups that ransomware cannot modify or delete.
•	Backup jobs are normally monitored automatically, with alerts sent if a backup fails.
•	Large organisations often store backups in multiple locations to improve resilience.
•	Backup files are usually encrypted to protect sensitive business data if the storage device is ever lost or stolen.
Understanding these differences helped me appreciate that my lab represents the core principles of enterprise backup rather than every enterprise feature.

Reflection
Writing this disaster recovery plan brought everything I had learned throughout the project together.
It made me think beyond simply configuring backup software and instead focus on how an organisation actually recovers from an outage.
By the end of this section, I wasn't just creating backups anymore—I was planning for real-world incidents, deciding how systems should be prioritised, documenting recovery procedures, measuring recovery times, and thinking about business continuity as a whole.
That shift in mindset was one of the most valuable things I gained from this project.
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/51c24adf-0137-447e-94cc-5002213f6ed0" />

Section 7  Backup Integrity Testing Record
Throughout this project, I made it a priority not to simply configure backups and assume they were working. Every time I completed a backup or recovery exercise, I recorded the results so I could track exactly what had been tested and how the system performed.

Keeping this record helped me prove that my backup strategy wasn't based on assumptions—it had been tested under different scenarios and consistently produced successful recoveries.

In a real organisation, records like these become part of the backup audit trail. They provide evidence that backup systems are not only configured correctly but are also tested regularly, which is an important requirement for many security standards and compliance frameworks.

Backup Integrity Test Log

Test Performed

Scenario

Result

Recovery Time

Full VM Restore — DC01

Simulated hardware failure

✅ Pass

22 minutes

File-Level Restore

Accidental folder deletion

✅ Pass

4 minutes

File-Level Restore

Simulated ransomware attack

✅ Pass

8 minutes

SureBackup Verification — DC01

Automated backup integrity verification

✅ Pass

12 minutes

Incremental Backup Integrity

CRC checksum verification

✅ Pass

N/A

Backup Copy Job

Secondary backup copy completed successfully

✅ Pass

N/A

What This Record Taught Me

Looking back at these results gave me a lot of confidence in the environment I had built.

Every recovery test completed successfully, whether I was restoring an entire virtual machine, recovering deleted files, or verifying backups automatically with SureBackup. More importantly, each test proved something different about the reliability of my backup strategy.

For example:

·        The full VM restore confirmed that I could recover an entire server after a complete hardware failure.

·        The file-level restores showed that I could recover individual files quickly without affecting the rest of the server.

·        The SureBackup verification proved that my backups weren't just stored—they were actually bootable and functional.

·        The backup copy job confirmed that I had a secondary copy of my backups, supporting the principles of the 3-2-1 backup strategy.

Reflection

One of the biggest lessons I learned from this project is that creating backups is only half the job.

The real value comes from testing them.

A backup that has never been restored is simply something you hope will work. A backup that has been restored, verified, and documented is one you can trust.

By the end of this project, I wasn't just able to say that I knew how to configure Veeam. I had successfully restored a Domain Controller after a simulated hardware failure, recovered deleted files, verified backup integrity automatically, and recovered from a simulated ransomware attack with zero data loss.

For me, this testing record is more than just a table of results—it's evidence that the backup solution I built actually works when it's needed most.
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/f090b944-ae8f-40c0-9880-407ecbe91baf" />

Section 8  Challenges I Encountered and What They Taught Me
No IT project goes perfectly from start to finish, and this one was no exception.
In fact, some of the biggest lessons I learned came from things not working the first time.
Whenever something failed, I tried not to immediately look for the answer. Instead, I spent time reading the error messages, checking logs, comparing settings, and understanding why it had happened before fixing it. That troubleshooting process taught me far more than if everything had worked on the first attempt.
Looking back, these challenges ended up being some of the most valuable parts of the entire project.

Challenge 1 — My First Backup Failed Because of VSS Permissions
The first time I tried to back up DC01, I expected everything to work because I had carefully followed the setup process.
Instead, the backup failed.
When I checked the Veeam job log, I noticed the failure happened during Application-aware Processing. The log mentioned a VSS Writer error along with insufficient permissions for guest processing.
At first, I wasn't entirely sure what that meant.
After spending some time researching the error and reviewing my Veeam settings, I realised the problem wasn't Veeam at all—it was the credentials I had provided.
I had configured Veeam to connect to the Domain Controller using an ordinary domain user account.
That wasn't enough.
Because Application-aware Processing communicates with Windows' Volume Shadow Copy Service (VSS) to create a consistent backup, it requires administrative privileges. On a Domain Controller, only a Domain Administrator has the permissions needed to perform those operations.
How I Fixed It
I edited the backup job and replaced the guest credentials with my LAB\Administrator account.
After running the backup again, everything completed successfully.
The logs confirmed that Application-aware Processing had finished correctly and the backup was created without any issues.
What I Learned
This experience taught me that choosing the correct credentials is just as important as configuring the backup itself.
A backup that skips Application-aware Processing may still complete, but it becomes a crash-consistent backup rather than an application-consistent one.
For services like Active Directory, that difference can determine whether a restore works properly or not.
From now on, whenever I configure backups for critical Windows servers, I always verify that the guest processing logs confirm VSS completed successfully before considering the backup trustworthy.

Challenge 2  My SureBackup Verification Timed Out
Once my backups were working, I moved on to testing them using SureBackup.
The first verification didn't go as planned.
The virtual machine powered on successfully, and the heartbeat test passed, but after several minutes Veeam reported that the Active Directory application test had timed out.
Initially, I thought something was wrong with my backup.
After investigating further, I realised the backup itself was perfectly healthy.
The real problem was much simpler.
Because my lab runs on a computer with only 8 GB of RAM, the virtual machines take longer to start than they would on enterprise hardware.
DC01 needed around eight minutes before Active Directory was fully operational.
However, SureBackup was only waiting five minutes before declaring the test a failure.
How I Fixed It
I increased the maximum boot timeout for the application group from 300 seconds to 600 seconds.
When I ran the verification again, SureBackup waited long enough for the Domain Controller to finish starting.
This time every verification test passed successfully.
What I Learned
This taught me not to jump to conclusions when something fails.
A failed verification doesn't always mean the backup is bad.
Sometimes the problem is simply that the testing environment isn't configured correctly.
Before assuming the worst, it's important to understand why a failure occurred.
Reading logs carefully can save a lot of unnecessary troubleshooting.

Challenge 3  My Restore Failed Because the Virtual Machine Already Existed
After successfully restoring DC01 once, I wanted to repeat the exercise so I could become more confident with the recovery process.
This second attempt produced another unexpected error.
Partway through the restore, Veeam stopped and reported that the virtual machine was already registered in the VMware inventory.
At first I thought the backup files were damaged.
They weren't.
The problem was entirely my own.
Between restore tests, I had powered DC01 back on.
Although the machine was running normally, it was still registered inside VMware.
When I told Veeam to restore the VM back to its original location, it tried to create another virtual machine with the same identity, causing a conflict.
How I Fixed It
Instead of deleting the virtual machine, I simply removed it from the VMware inventory.
Once VMware no longer had an existing registration for DC01, I started the restore again.
This time the process completed successfully.
I also learned that another option would have been restoring the server to a completely different location under a new name.
What I Learned
This challenge showed me that successful disaster recovery isn't only about knowing how to restore backups.
It's also about understanding how the virtualisation platform behaves.
Before restoring a machine to its original location, it's important to make sure VMware no longer has the old virtual machine registered.
Missing this small step could delay recovery during a real incident.

Reflection
Although these issues slowed me down, I'm actually glad they happened.
If every backup, restore, and verification had worked perfectly on the first attempt, I would have learned how to follow instructions—but not how to troubleshoot.
Instead, I gained experience reading logs, interpreting error messages, identifying root causes, and making informed fixes.
Those troubleshooting skills are just as valuable as knowing how to configure the software in the first place.
By the end of this project, I wasn't just more confident using Veeam—I was much more comfortable investigating problems, understanding why they occurred, and resolving them systematically. Those are skills I'll carry into any future IT support or systems administration role.

Results I Achieved
The practical testing throughout the project allowed me to measure how well my backup strategy performed.
Metric	Result
Recovery Point Objective (RPO)	24 hours
Recovery Time Objective (Measured)	22 minutes for a full virtual machine restore
File-Level Recovery Time	4–8 minutes
Restore Tests Completed	6
Restore Success Rate	100%
Data Loss During Testing	Zero
Ransomware Recovery	Successful
Backup Isolation	Verified
Seeing these figures together was rewarding because they weren't estimates—they were measured during real recovery tests that I carried out myself.

What This Project Demonstrates
More than anything, this project demonstrates that I understand backup and disaster recovery as a complete process rather than simply a piece of software.
I learned how to install and configure Veeam, design backup jobs, choose appropriate retention policies, protect Active Directory with application-aware backups, verify backup integrity, recover both entire virtual machines and individual files, and respond to simulated ransomware incidents.
Perhaps the biggest lesson I took away is that backups should never be judged by whether they complete successfully—they should be judged by whether they can be restored successfully.
Every major backup I created during this project was tested, verified, and documented.
That experience has given me practical confidence in performing backup and recovery tasks, troubleshooting issues when they arise, and understanding the decisions that go into building a reliable disaster recovery strategy.
Looking back, this project wasn't simply about learning Veeam. It was about developing the mindset of an IT professional who understands that protecting systems is only half the job the real responsibility is making sure those systems can be recovered quickly, safely, and with minimal disruption when something goes wrong.







