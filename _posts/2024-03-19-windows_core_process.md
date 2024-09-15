---
title: Windows Core Process
date: 2024-03-19 08:25:41 AM
categories: [Blue Team]
tags: [blueteam] 
media_subpath: /assets/img/blueteam/windows_core_process
authors: [0]
---

## Windows Core Process


[Download the **S**ANS **D**FIR](/assets/pdf/windows_core_process/sans_dfir.pdf)

### Overview From SANS
![Windows Core Process](processes.png)

### system.exe
> The System process is responsible for most kernel-mode threads.
Modules run under System are primarily drivers (.sys files), but also include
several important DLLs as well as the kernel executable, ntoskrnl.exe
{: .prompt-tip }
![Task Manager View](system_normal.png)
![Process Hack View](system_processhack.png)
- **Image Path**: **N/A** for system.exe – Not generated from an executable image
- **Parent Process**: None / System Idle Process (0)
- **Number of Process**: 1
- **User Account**: Local System
- **Start Time**: At boot time
- **Description**: <br>
The System process (process ID 4) is the home for a special kind of thread that runs only in kernel mode a kernel-mode system thread.
System threads have all the attributes and contexts of regular user-mode threads (such as a hardware context, priority, and so on) but are different in that they run only in kernel-mode executing code loaded in system space, whether that is in Ntoskrnl.exe or in any other loaded device driver.
In addition, system threads don't have a user process address space and hence must allocate any dynamic storage from operating system memory heaps, such as a paged or nonpaged pool.

> What is unusual behaviour for this process?
- A parent process (aside from System Idle Process (0))
- Multiple instances of System. (Should only be one instance) 
- A different PID. (Remember that the PID will always be PID 4)
- Not running in Session 0
{: .prompt-info }

### smss.exe
> The Session Manager process is responsible for creating new
sessions. The fi rst instance creates a child instance for each new session.
Once the child instance initializes the new session by starting the Windows
subsystem (csrss.exe) and wininit.exe for Session 0 or winlogon.exe
for Session 1 and higher, the child instance exits.
{: .prompt-tip }
![Task Manager View](smss.png)
- **Image Path**: %SystemRoot%\System32\smss.exe
- **Parent Process**: System.exe
- **Number of Process**: One master instance and another child instance per
session. Children exit after creating their session.
- **User Account**: Local System
- **Start Time**: Within seconds of boot time for the master instance
- **Description**: <br>
This process, also known as the Windows Session Manager, is responsible for creating new sessions. It is the first user-mode process started by the kernel. This process starts the kernel and user modes of the Windows subsystem. This subsystem includes win32k.sys (kernel mode), winsrv.dll (user mode), and csrss.exe (user mode). Smss.exe starts csrss.exe (Windows subsystem) and wininit.exe in Session 0, an isolated Windows session for the operating system, and csrss.exe and winlogon.exe for Session 1, which is the user session. The first child instance creates child instances in new sessions, done by smss.exe copying itself into the new session and self-terminating. 

>What is unusual?
- A different parent process other than System (4)
- The image path is different from C:\Windows\System32
- More than one running process. (children self-terminate and exit after each new session)
- The running User is not the SYSTEM user
- Unexpected registry entries for Subsystem (Any other subsystem listed in the Required value of `HKLM\System\CurrentControlSet\Control\Session Manager\Subsystems` )
{: .prompt-info }

### csrss.exe
> csrss.exe (Client Server Runtime Process) is the user-mode side of the Windows subsystem. This process is always running and is critical to system operation. If this process is terminated by chance, it will result in system failure. This process is responsible for the Win32 console window and process thread creation and deletion. For each instance, csrsrv.dll, basesrv.dll, and winsrv.dll are loaded (along with others). 
This process is also responsible for making the Windows API available to other processes, mapping drive letters, and handling the Windows shutdown process. 
{: .prompt-tip }

> Recall that csrss.exe and winlogon.exe are called from smss.exe at startup for Session 1. 
{: .prompt-danger }
![Task Manager View](csrss-session.png)

- **Image Path**: %SystemRoot%\System32\csrss.exe
- **Parent Process**: : Created by an instance of **smss.exe** that exits, so analysis tools usually do not provide the parent
- **Number of Process**: Two or more
- **User Account**: Local System
- **Start Time**: Within seconds of boot time for the first two instances (for Session 0 and 1). Start times for additional instances occur as new sessions are created, although often only Sessions 0 and 1 are created
- **Description**: <br>
The Client/Server Run-Time Subsystem is the user-mode process for the Windows subsystem. Its duties
include managing processes and threads, importing many of the DLLs that provide the Windows API, and facilitating
shutdown of the GUI during system shutdown. An instance of **csrss.exe** will run for each session. Session 0 is for
services and Session 1 for the local console session. Additional sessions are created through the use of Remote
Desktop and/or Fast User Switching. Each new session results in a new instance of **csrss.exe**. 

>What is unusual?
- An actual parent process. (smss.exe calls this process and self-terminates)
- Image file path other than C:\Windows\System32
- Subtle misspellings to hide rogue processes masquerading as csrss.exe in plain sight
- The user is not the SYSTEM user.
{: .prompt-info }

### wininit.exe
> The Windows Initialization Process, wininit.exe, is responsible for launching services.exe (Service Control Manager), lsass.exe (Local Security Authority), and lsaiso.exe within Session 0. It is another critical Windows process that runs in the background, along with its child processes. 
{: .prompt-tip }
> Note: lsaiso.exe is a process associated with **Credential Guard and KeyGuard**. You will only see this process if Credential Guard is enabled. 
{: .prompt-danger }
![Task Manager View](winint.png)
- **Image Path**: %SystemRoot%\System32\wininit.exe
- **Parent Process**: Created by an instance of **smss.exe** that exits, so tools
usually do not provide the parent process name.
- **Number of Process**: One
- **User Account**: Local System
- **Start Time**: Within seconds of boot time
- **Description**: <br>
Wininit.exe starts key background processes within Session 0.
It starts the Service Control Manager (**services.exe**), the Local Security
Authority process (**lsass.exe**), and **lsaiso.exe** for systems with Credential
Guard enabled. Note that prior to Windows 10, the Local Session Manager
process (**lsm.exe**) was also started by **wininit.exe**. As of Windows 10, that
functionality has moved to a service DLL (**lsm.dll**) hosted by svchost.exe. 

>What is unusual?
- An actual parent process. (smss.exe calls this process and self-terminates)
- Image file path other than C:\Windows\System32
- Subtle misspellings to hide rogue processes in plain sight
- Multiple running instances
- Not running as SYSTEM
{: .prompt-info }

### RuntimeBroker.exe
- **Image Path**: %SystemRoot%\System32\RuntimeBroker.exe
- **Parent Process**: svchost.exe
- **Number of Process**: One or more
- **User Account**: Typically the logged-on user(s)
- **Start Time**: Start times vary greatly
- **Description**: <br>
RuntimeBroker.exe acts as a proxy between the constrained
Universal Windows Platform (UWP) apps (formerly called Metro apps) and the
full Windows API. UWP apps have limited capability to interface with hardware
and the fi le system. Broker processes such as RuntimeBroker.exe
are therefore used to provide the necessary level of access for UWP
apps. Generally, there will be one RuntimeBroker.exe for each UWP
app. For example, starting **Calculator.exe** will cause a corresponding
RuntimeBroker.exe process to initiate.

### taskhostw.exe
- **Image Path**: %SystemRoot%\System32\taskhostw.exe
- **Parent Process**: svchost.exe
- **Number of Process**: One or more
- **User Account**: Multiple taskhostw.exe processes are normal. One or more may
be owned by logged-on users and/or by local service accounts.
- **Start Time**: Start times vary greatly
- **Description**: <br>
The generic host process for Windows Tasks. Upon initialization,
taskhostw.exe runs a continuous loop listening for trigger events. Example
trigger events that can initiate a task include a defi ned schedule, user logon,
system startup, idle CPU time, a Windows log event, workstation lock, or
workstation unlock.
There are more than 160 tasks preconfi gured on a default installation of
Windows 10 Enterprise (though many are disabled). All executable fi les (DLLs &
EXEs) used by the default Windows 10 scheduled tasks are signed by Microsoft.

### winlogon.exe
> The Windows Logon, winlogon.exe, is responsible for handling the **Secure Attention Sequence** (SAS). It is the ALT+CTRL+DELETE key combination users press to enter their username & password. This process is also responsible for loading the user profile. It loads the user's NTUSER.DAT into HKCU, and userinit.exe loads the user's shell.
{: .prompt-tip }
![Task Manager View](winlogon.png)
![Task Manager View](winlogon_session.png)
- **Image Path**: %SystemRoot%\System32\winlogon.exe
- **Parent Process**: Created by an instance of **smss.exe** that exits, so analysis
tools usually do not provide the parent process name.
- **Number of Process**: One or more
- **User Account**: Local System
- **Start Time**: Within seconds of boot time for the fi rst instance (for Session 1).
Start times for additional instances occur as new sessions are created, typically
through Remote Desktop or Fast User Switching logons.
- **Description**: <br>
Winlogon handles interactive user logons and logoffs. It launches
LogonUI.exe, which uses a credential provider to gather credentials from the
user, and then passes the credentials to lsass.exe for validation. Once the
user is authenticated, Winlogon loads the user’s NTUSER.DAT into HKCU and
starts the user’s shell (usually explorer.exe) via userinit.exe.

> What is unusual?
- An actual parent process. (smss.exe calls this process and self-terminates)
- Image file path other than C:\Windows\System32
- Subtle misspellings to hide rogue processes in plain sight
Not running as SYSTEM
- Shell value in the registry other than explorer.exe `Computer\HKEY_LOCAL_machine\Software\Microsoft\Windows NT\CurrentVersion\Winlogon`
{: .prompt-info }

### services.exe
> the Service Control Manager (SCM) or services.exe. Its primary responsibility is to handle system services: loading services, interacting with services and starting or ending services. It maintains a database that can be queried using a Windows built-in utility, `sc.exe`.
{: .prompt-tip }
![Windows Core Process](services.png)
![Windows Core Process](services_session.png)
- **Image Path**: %SystemRoot%\System32\services.exe
- **Parent Process**: wininit.exe
- **Number of Process**:  One
- **User Account**: Local System
- **Start Time**: Within seconds of boot time
- **Description**: <br>
Implements the Unifi ed Background Process Manager (UBPM), which is responsible for background
activities such as services and scheduled tasks. Services.exe also implements the Service Control Manager (SCM),
which specifi cally handles the loading of services and device drivers marked for auto-start. In addition, once a user
has successfully logged on interactively, the SCM (services.exe) considers the boot successful and sets the Last
Known Good control set (HKLM\SYSTEM\Select\LastKnownGood) to the value of the CurrentControlSet.

>What is unusual?
- A parent process other than wininit.exe
- Image file path other than C:\Windows\System32
- Subtle misspellings to hide rogue processes in plain sight
- Multiple running instances
- Not running as SYSTEM
{: .prompt-info }

### svchost.exe
> The Service Host (Host Process for Windows Services), or svchost.exe, is responsible for hosting and managing Windows services. 
![svchost tree](svchost_tree.png)
{: .prompt-tip }

![svchost tree](svchost.png)
- **Image Path**: %SystemRoot%\system32\svchost.exe
- **Parent Process**: services.exe (most often)
- **Number of Process**: Many (generally at least 10)
- **User Account**: Varies depending on svchost instance, though it typically will be Local System, Network Service, or
Local Service accounts. Windows 10 also has some instances running as logged-on users
- **Start Time**: Typically within seconds of boot time. However, services can be started after boot (e.g., at logon), which
results in new instances of svchost.exe after boot time.
- **Description**: <br>
Generic host process for Windows services. It is used for running service DLLs. Windows will
run multiple instances of svchost.exe, each using a unique “-k” parameter for grouping similar services.
Typical “-k” parameters include DcomLaunch, RPCSS, LocalServiceNetworkRestricted, LocalServiceNoNetwork,
LocalServiceAndNoImpersonation, netsvcs, NetworkService, and more. Malware authors often take advantage of the
ubiquitous nature of svchost.exe and use it either to host a malicious DLL as a service, or run a malicious process
named svchost.exe or similar spelling. Beginning in Windows 10 version 1703, Microsoft changed the default
grouping of similar services if the system has more than 3.5 GB of RAM. In such cases, most services will run under
their own instance of svchost.exe. On systems with more than 3.5 GB RAM, expect to see more than 50 instances of
svchost.exe (the screenshot in the poster is a Windows 10 VM with 3 GB RAM)

> What is unusual?
- A parent process other than services.exe
- Image file path other than C:\Windows\System32
- Subtle misspellings to hide rogue processes in plain sight
- The absence of the -k parameter
{: .prompt-info }

### lsaiso.exe
- **Image Path**: %SystemRoot%\System32\lsaiso.exe
- **Parent Process**: wininit.exe
- **Number of Process**: Zero or one
- **User Account**: Local System
- **Start Time**: Within seconds of boot time
- **Description**: <br>
When Credential Guard is enabled, the functionality of lsass.exe is split between two processes –
itself and lsaiso.exe. Most of the functionality stays within lsass.exe, but the important role of safely storing
account credentials moves to lsaiso.exe. It provides safe storage by running in a context that is isolated from other
processes through hardware virtualization technology. When remote authentication is required, lsass.exe proxies
the requests using an RPC channel with lsaiso.exe in order to authenticate the user to the remote service. Note
that if Credential Guard is not enabled, lsaiso.exe should not be running on the system. 

> What is unusual?
- A parent process other than wininit.exe
- Image file path other than C:\Windows\System32
- Subtle misspellings to hide rogue processes in plain sight
- Multiple running instances
- Not running as SYSTEM
{: .prompt-info }

### lsass.exe
> Local Security Authority Subsystem Service (LSASS) is a process in Microsoft Windows operating systems that is responsible for enforcing the security policy on the system. It verifies users logging on to a Windows computer or server, handles password changes, and creates access tokens. It also writes to the Windows Security Log.
`It creates security tokens for SAM (Security Account Manager), AD (Active Directory), and NETLOGON. It uses authentication packages specified in HKLM\System\CurrentControlSet\Control\Lsa`.
{: .prompt-tip }
![lsass](lsass.png)
- **Image Path**: %SystemRoot%\System32\lsass.exe
- **Parent Process**: wininit.exe
- **Number of Process**: One
- **User Account**: Local System
- **Start Time**: Within seconds of boot time
- **Description**: <br>
The Local Security Authentication Subsystem Service process is responsible for authenticating users by
calling an appropriate authentication package specifi ed in HKLM\SYSTEM\CurrentControlSet\Control\Lsa.
Typically, this will be Kerberos for domain accounts or MSV1_0 for local accounts. In addition to authenticating
users, lsass.exe is also responsible for implementing the local security policy (such as password policies and
audit policies) and for writing events to the security event log. Only one instance of this process should occur and it
should not have child processes.

>What is unusual?
- A parent process other than wininit.exe
- Image file path other than C:\Windows\System32
- Subtle misspellings to hide rogue processes in plain sight
- Multiple running instances
- Not running as SYSTEM
{: .prompt-info }

### explorer.exe
>  explorer.exe. This process gives the user access to their folders and files. It also provides functionality for other features, such as the Start Menu and Taskbar.
As mentioned previously, the Winlogon process runs userinit.exe, which launches the value in `HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\Shell` . Userinit.exe exits after spawning explorer.exe. Because of this, the parent process is non-existent. 
{: .prompt-tip }
- **Image Path**: %SystemRoot%\explorer.exe
- **Parent Process**: Created by an instance of userinit.exe that exits, so analysis tools usually do not provide the
parent process name.
- **Number of Process**: One or more per interactively logged-on user
- **User Account**: <logged-on user(s)>
- **Start Time**: First instance starts when the owner’s interactive logon begins
- **Description**: <br>
At its core, Explorer provides users access to fi les. Functionally, though, it is both a fi le browser
via Windows Explorer (though still explorer.exe) and a user interface providing features such as the user’s
Desktop, the Start Menu, the Taskbar, the Control Panel, and application launching via fi le extension associations
and shortcut fi les. Explorer.exe is the default user interface specifi ed in the Registry value `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Shell` ,though Windows can alternatively function with another interface such as cmd.exe or powershell.exe. Notice that the legitimate explorer.exe resides in the
`%SystemRoot%` directory rather than `%SystemRoot%\System32` . Multiple instances per user can occur, such as
when the option "Launch folder windows in a separate process" is enabled.

> What is unusual?
- An actual parent process. (userinit.exe calls this process and exits)
- Image file path other than C:\Windows
- Running as an unknown user
- Subtle misspellings to hide rogue processes in plain sight
- Outbound TCP/IP connections
{: .prompt-info }

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