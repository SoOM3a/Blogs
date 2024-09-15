---
title: SMB Enumeration
date: 2024-03-12 08:25:41 PM
categories: [RedTeam, Enumeration]
tags: [redteam]     # TAG names should always be lowercase
authors: [0]
media_subpath: /assets/img/services_enumerations/smb/
---


## SMB Architecture
SMB - Server Message Block Protocol - is a client-server communication protocol used for sharing access to files, printers, serial ports and other resources on a network.
The SMB protocol is known as a response-request protocol, meaning that it transmits multiple messages between the client and server to establish a connection. Clients connect to servers using TCP/IP (actually NetBIOS over TCP/IP as specified in RFC1001 and RFC1002), NetBEUI or IPX/SPX.

![smb archtiecture](smb_archtiecture.png)

## Enumeration using enum4linux
The syntax of Enum4Linux is nice and simple: 


```bash
enum4linux [options] ip
```


| Tag                                               |  Function   |
| :-------------------------------------            | ----------: |
| get userlist                                      |       -U    |
| get machine list                                  |       -M    |
| get namelist dump (different from -U and-M)       |       -N    |
| get sharelist                                     |       -S    |
| get password policy information                   |       -P    |
| get group and member list                         |       -G    |
| all of the above (full basic enumeration)         |       -a    |


![enum3linx CheatSheet1](enum4linux_smb_cheatsheet_1.png)
![enum3linx CheatSheet2](enum4linux_smb_cheatsheet_2.png)

# refernces 
- https://medium.com/@gregIT/exploiting-simple-network-services-in-ctfs-ec8735be5eef
- https://attack.mitre.org/techniques/T1210/
- https://www.nextgov.com/cybersecurity/2019/10/nsa-warns-vulnerabilities-multiple-vpn-services/160456/


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