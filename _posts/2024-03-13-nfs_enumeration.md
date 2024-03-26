---
title: NFS Enumeration
date: 2024-03-13 08:25:41 AM
categories: [RedTeam, Enumeration]
tags: [redteam]     # TAG names should always be lowercase
authors: [0]
img_path: /assets/img/services_enumerations/nfs/
---

## NFS 
![nfs architecture](nfs_architecture.ppm)

First, the client will request to mount a directory from a remote host on a local directory just the same way it can mount a physical device. The mount service will then act to connect to the relevant mount daemon using RPC.

The server checks if the user has permission to mount whatever directory has been requested. It will then return a file handle which uniquely identifies each file and directory that is on the server.

If someone wants to access a file using NFS, an RPC call is placed to NFSD (the NFS daemon) on the server. This call takes parameters such as:

-  The file handle
-  The name of the file to be accessed
-  The user's, user ID
-  The user's group ID

These are used in determining access rights to the specified file. This is what controls user permissions, I.E read and write of files.

### What runs NFS?
Using the NFS protocol, you can transfer files between computers running Windows and other non-Windows operating systems, such as Linux, MacOS or UNIX. And it can be run on (windows, Linux) OSs. 

### Access Mount NFS 
```bash
sudo mount -t nfs IP:share /tmp/mount/ -nolock
```

| Argument          |  Function                                                                              |
| :---------------- | -------------------------------------------------------------------------------------: |
|mount             | Execute the mount command                                                               |
| -t nfs           | Type of device to mount, then specifying that it's NFS                                  |
| IP:share         | The IP Address of the NFS server, and the name of the share we wish to mount            |
| -nolock          | Specifies not to use NLM locking                                                        |

### Exploitation Notes

#### root_squash

By default, on NFS shares- Root Squashing is enabled, and prevents anyone connecting to the NFS share from having root access to the NFS volume. Remote root users are assigned a user “nfsnobody” when connected, which has the least local privileges. Not what we want. However, if this is turned off, it can allow the creation of SUID bit files, allowing a remote user root access to the connected system.

##### SUID
So, what are files with the SUID bit set? Essentially, this means that the file or files can be run with the permissions of the file(s) owner/group. In this case, as the super-user. We can leverage this to get a shell with these privileges!

#### Method
This sounds complicated, but really- provided you're familiar with how SUID files work, it's fairly easy to understand. We're able to upload files to the NFS share, and control the permissions of these files. We can set the permissions of whatever we upload, in this case a bash shell executable. We can then log in through SSH, as we did in the previous task- and execute this executable to gain a root shell!

```bash
# it could add s bit for group or user and we can add for both
chmod +s file
# user
chmod u+s file
# group
chmod g+s file

```
	
