---
title: Windows DFIR Notes
date: 2024-03-19 08:25:41 AM
categories: [Blue Team]
tags: [blueteam] 
media_subpath: /assets/img/blueteam/windows_dfir
authors: [0]
---
> Note: any blog with word `Notes`, it designed to help/address commands or steps need in dfir to quick recall things.
{: .prompt-tip }
## Evidence of execution

### Sysmon 
  - **eventId**: 4688
  - **channel**: system 
  - **description**: process execution

### Preftech 
- **Location**: `C:\Windows\Prefetch`
- **Kind**: `.pf`
- **Tool Zimmerman**:  `PEcmd -f/-d --csv`
- **description**: Process execution
- **Evidence Information(EI)**: 
  - File Name - (Hased Location)
  - Time Created/Timed Modified
  - saving 7 times of Run + LastRun = 8 Run
  - refernce Files
    **Notes**: 
        - Specially Process (Multiple Entries): `mmc.exe`, `svchost.exe`, `rundllhost.exe`, `rundll32.exe`
        - `Sdelete.pf` will still show delete pf files which deleted
- [**Resource**](https://www.magnetforensics.com/blog/forensic-analysis-of-prefetch-files-in-windows/)

### Shimcache (Appcompat) 
- **Location**: `HLM\SYSTEM\CurrentControlSet\Control\Session Manager\AppCompatCache`
- **Kind**: registry hive
- **Tool Zimmerman**: `AppCompatCacheParser.exe -f/-d --csv` Or `WinPmem (other tool for memdump)`
- **description**: in older windows version containing flag of `executed` in modern windows version, it means the user navigate to this path.
- **Evidence Information(EI)**: 
  - Evidence of File Exsistenece in modern Windows OS (Win10H2)
  - TimeStamping for Time of Modification
- **Note**: 
  - make sure you are using latest ControlSet `HLM\SYSTEM\Select`
  - Rename/Move not affecting time modification in ShimCache, We can check 64 timestamp to detect rename files
  - Shimcache will only saved on system after reboot (1024 entries only)
- [**Resource**](https://www.magnetforensics.com/blog/shimcache-vs-amcache-key-windows-forensic-artifacts/)

### Amcache
 - **Kind**: registry hive
 - **Location**: `C:\Windows\appcompat\Programs`
 - **Tool Zimmerman**: `Registry Explorer` OR `AppCompatCacheParser.exe -f/-d --csv`
 - **description**: metadata of installed software
 - **Evidence Information(EI)**: 
  - **InventoryApplication**: ProgramId(sha1)/programName/Publisher/Version/compilationDate->LinkDate
  - **InventoryApplicationFile**: Standlone Applications
  - **InventoryDriver**: Driver installed
- **Note**: old Win7 name: recentFileCache 
- [**Resource**](https://www.magnetforensics.com/blog/shimcache-vs-amcache-key-windows-forensic-artifacts/)

### MRU (Most Recently Used)
 - **Kind**: `NTUSER.data::registry hive`
 - **Location**: `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs`
 - **Tool Zimmerman**: `Registry Explorer`
 - **description**: recently open files/documents
 - **Evidence Information(EI)**: 
  - Recently opened files, and location
- **Note**:
  - folders that are accessed by a Windows application using the common Open / Save dialog 
    - `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32`
  - files that are accessed by a Windows application using the common Open File or Save File dialog
    - `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32\OpenSavePidlMRU`
  -  items of interest regarding commands a user runs via the Windows Run utility
    - `NTUSER.DAT\ Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU`
- [**Resource**](https://www.magnetforensics.com/blog/what-is-mru-most-recently-used/)

### PCA (Program Compatibility Assistant)
 - **Kind**: `.txt`
 - **Location**: `C:\Windows\appcompat\pca`
 - **Tool Zimmerman**: `visual stuido Code` OR `Any text editor`
 - **description**: process running with command and OS exit codes for the process
 - **Evidence Information(EI)**: 
  - Full Path of excutable|time of execution
  - ProgramId (`0000sha1`)
  - OS process exit codes

### MuiCache
 - **Location**: `HKCU\SOFTWARE\Classes\Local Settings\Software\Microsoft\Windows\Shell\MuiCache`
 - **Kind**: registry hive
 - **Tool Zimmerman**: `reg Explorer`
 - **description**: Process execution by user in GUI
 - **Evidence Information(EI)**: 
   - GUI program which run per user
 - **Note**: 
   - No Time tracking recorded
- [**Resource**](https://www.magnetforensics.com/blog/forensic-analysis-of-muicache-files-in-windows/)

### UserAssist
 - **Location**: `HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`
 - **Kind**: registry hive
 - **Tool Zimmerman**: `reg Explorer`
 - **description**: records metadata relating to applications with a GUI component
 - **Evidence Information(EI)**: 
   - GUI program which run per user
   - focus time/Last execution/count
 - **Note**: 
   - count could be 0, as service can run process in user context
   - entires decide with `ROT13`
- [**Resource**](https://www.cybertriage.com/blog/userassist-forensics-2025/)

### SRUM (Source utilization utilization monitor)
 - **Location**: `C:\Windows\System32\sru`
 - **Kind**: `.dat :: database file`
 - **Tool Zimmerman**: `srumcmd.exe` 
 - **description**: a feature of modern Windows systems (Win8+), intended to track the application usage, network utilization and system energy state.
 - **Evidence Information(EI)**: 
        - AppResourceuserInfo / AppTimelineProvide / NetworkConnection / NetworkUsage
 - **Note**: 
        - count could be 0, as service can run process in user context
        - need Software hive (System32/Config/Software) / SRUM to makes too work
- [**Resource**](https://www.magnetforensics.com/blog/srum-forensic-analysis-of-windows-system-resource-utilization-monitor/)




> 
- For Hands-on in different type of cases free
 - [Workshop -Investigating Insider Threats](https://training.sleuthkitlabs.com/courses/workshop-investigating-insider-threats)
 - [Workshop - Investigating Data Exfiltration](https://training.sleuthkitlabs.com/courses/workshop-investigating-data-exfiltration)
 - [Workshop - Investigating Ransomware](https://training.sleuthkitlabs.com/courses/workshop-investigating-ransomware)
{: .prompt-tip }

## Evidence of presistence
 - **Installed services**: `Commandline/powershell events`
 - **Scheduled Tasks**: `C:\Windows\System32\Tasks`
 - **Autostart**:
```
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce
   ```
## Evidence of Leteral Movment
> most common tools/protocols which we can search for (SMB,RDP, WMI, Psexec)
{: .prompt-tip }
### **RDP**: 
 - EventId:
    ```4624 - Type 3 Network
    5140
    5145
    5156```
### **SMB**:
 - shellbags:  `HKCU\SOFTWARE\Classes\Local Settings\Software\Microsoft\Windows\Shell\BagMRU and Bags`
 - HIPS/IPS/IDS Triggers on SMB port
### **WMI**
 - Microsoft-WMIOperational
### **Powershell remoting/WinRM**:
 - event Log channels
  - `Microsoft-Windows-PowerShell/Operational`
  - `Windows-PowerShell`
  - `MicrosoftWinRmOperional`

## Privilige Escalation 
 - Lsass dump -> `mimikatz` (memory/pass the hash/pass the ticket/Golden Ticket)
     - ntd.dit
     - WDigest (legacy) (useloginCredentials)
 - Anomaly detections using common techniques: 
    - Process Tracking 
    - Config.msi 
    - File OwnerShip

<script src="https://giscus.app/client.js"
        data-repo="SoOM3a/Blogs"
        data-repo-id="R_kgDOLebVZA"
        data-category="General"
        data-category-id="DIC_kwDOLebVZM4Cd9IX"
        data-mapping="url"
        data-strict="1"
        data-reactions-enabled="1"
        data-emit-metadata="1"
        data-input-position="top"
        data-theme="preferred_color_scheme"
        data-lang="en"
        data-loading="lazy"
        crossorigin="anonymous"
        async>
</script>