---
title: Memory Forensics Notes
date: 2025-05-25 12:15:06 AM
categories: [Blue Team]
tags: [blueteam, dfir] 
media_subpath: /assets/pdf/difr/mix
authors: [0]
---
> Note: any blog with word `Notes`, it designed to help/address commands or steps need in dfir to quick recall things.
{: .prompt-tip }

## Logs `/var/logs`

### Auth
 - `/var/log/auth.og` (debian/ubuntu) - 4 weeks rentintion policy 
 - `/var/log/secure` (centos/rhel/ rokkieLinux/Fodera) - 4 weeks rentintion policy 

### Extra for Auth 
 - `/var/log/faillog` - (ubuntu/debian) disabled by default and only exisit before `pam-tali-2`
 - `/var/log/lastlog` - all distros enabled bydefault 
 - `/var/log/btmp` (bad login/reboots/shutdown) - lastb
 - `/var/log/wtmp` (successfull logins)- last
 - `/run/utmp` (current user session information)

### Syslog `syslog-ng & Rsyslog`
 - `/var/log/syslog` - (Ubuntu/debian) - ALl users can read this file 
 - `/vay/log/messages` - (RHEL/RoockyLinux/Fodera) - Only root can read this file 

### kernel operation
 - `/var/log/dmesg` - (Ubuntu/debian) - current content from kernelg ring buffer for most recent boot
 - `/var/log/kern.log` - (Ubuntu/debian) - logs during boot process to the disk which is huristic data for booting
 - `/var/bin/dmesg` - (fodera-rhel-rockyLinx) - a live command which display dmesg logs - (no dmesg logs file)
 - `journal` - (`/var/logs/journal`, `/etc/systemd/journald.conf` , `/run/var/journal`) 

### WebServers 
 - Apache - CLF(format) 
   - `/var/log/httpd/` (access_log, ssl_access_log, ssl_request_log, error_log, ssl_error_log ) - (Fodera/RHEL/CENTOs)
   - `/var/log/apache2` (access_log, error_log, other_vhosts_access ) - (Debian, Ubuntu) 
 - Nginx
   - `/var/log/nginx` (access.log, error.log) (ssl_access.log - not by default configured) - All Linux Distributions
 - tomcat
   - `/opt/tomcat/logs/catalina.out` - all stdout/err messages
   - `/opt/tomcat-logs/*.access_log` - WebServer Access Logs 
   - `/opttomcat/logs` (manager,host-manager) - logs for applications deployments
 - Other Logs 
   - Virutal hosts log files (server blocks)

> Note For Threat-Hunting: some ideas to track webshell on server:
 - unusall HTTP Methods 
 - repeated requests to specific files 
 - abnormal URI patterns (base64,php ,obsufcated name )
 - error codes 
 - access from know malicious IP address 
 - out-of-hours activity 
 - user agent strings 
 - refereer Field analomlies 
 - suspicious file uploads (php, double extensions)
{: .prompt-tip }

### Firewalls 
 - ipchains (linux kernel 2.2+) saved to (`/var/log` messages or kernel)
 - iptables (linux kernel 2.4+)
   - rules configurations - `/etc/iptables/` (rules.v4/rules.v6)
   - logging to (`/var/log` messages or kernel)
 - shorewall (built on iptables)
   - configurations file `/etc/shorewall`
   -  logging to (`/var/log` messages or kernel)
 - uncomplicated Firewalls (based on iptables)
   - logging to `/var/log/ufw.log`
 - FireHol (use of iptables and nftables)
   - configurations file `/etc/firehol/firehol.con`
   -  logging to (`/var/log` messages or kernel)
 - nftables (successor to iptables)
   - logging can be viwed with (journalctl -k) and logs might be wrriten to (`/var/log` messages or kernel)
 - Proxy Server
   - Squid
     - Configurations to `/etc/Squid`
     - logging to `/var/log/squid/access.log` and `cache.log`
   - HAProxt 
     - configurations saved in `/etc/haproxy `
     - logging to `/var/log/haproxy`
   - Nginx reverse proxy 
     - configuration in `/etc/nginx` 
     - logging to `/var/log/nginx/access.log` or `error.log `
   - cilium (Network Continerzation security based on ebpf)
     - Configurations in `/etc/cilium`
     - logging to `/var/log/cilium`
### Auditd (syscalls, file access, network activitiy) 
 - logs can be viewed (ausearch -k  )
### Sysmon
 - Configurations `/opt/sysmon/config.xml`
 - Logging to (`/var/log` messages/syslog)
 - Logs eventIds:
   - `*` Process Creation - #1
   - Network Connection - #3
   - Service State Change - #4
   - `*` Process Termination - #5
   - Raw Access Read - #9
   - Access Process - #10
   - File Create - #11
   - Service Configuration Change - #16
   - File Deletion - #23
   > Note: `*` logged by default 

### Vmware ESXI/Vcenter 
 - ESXI
   - Logs will be under `/var/log` multiple files, please visit [Resource](https://knowledge.broadcom.com/external/article/306962/location-of-esxi-log-files.html)
 - Vcenter 
   - Logs will be in [Resource](https://knowledge.broadcom.com/external/article/312194/location-of-vcenter-server-log-files.html)
     - Windows `%AllUsersProfile%\Vmware\VcenterServer\Logs\Vmware-vpx\vpxd.log`
     - Linux `/var/log/vmware/`

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