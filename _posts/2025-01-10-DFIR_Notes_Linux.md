---
title: Linux DFIR Notes
date: 2025-01-10 08:25:41 pm
categories: [Blue Team]
tags: [blueteam, dfir] 
media_subpath: /assets/pdf/difr/linux
authors: [0]
---
> Note: any blog with word `Notes`, it designed to help/address commands or steps need in dfir to quick recall things.
{: .prompt-tip }

## Evidences Collections

### Creating full disk image (dd, ddcfldd, dc3dd)
 - `dd if=<inputhard> of=<outputLocation> bs=4m`
 - `dcfldd if=<inputhard> of=<outputLocation> bs=4m hash=sha256 hashwindows=1G`
 - `dc3dd if=<inputhard> of=<outputLocation> log=<logFile> hash=sha256 hlog=<logFile2>`

> **Note**: watching files  `watch -n 1 -c ls -la`

### Creating  Memory dmp 
 - Microsoft AVLM - `./avlm filename.dmp`
 - Gerting only Artificates `./uac -p ir_triage <OutputTrigarLocation>`
> **Note**: UAC `Unix-Like Artificate Collector` support the following OSs Soalries/Andriod/ESXi/FreeBSD/Linux/Netscaler/OpenBsd/NetBSD/Aix
 - Using hypervisors level 
   -  Vmwware by taking snapeshot (with memory check enabled)
   > **Note**: 
   >  - need to download .vmem and .vmsn together for memory analysis.
   >  - need to download .vmdk while machine is down to get the full disk image.

   - HyberV
     - memory, u should taking a checkpoint with (enable checkpoints/Standered CheckPoints)
     - disk, should use export for the hardisk

## Mounting Disk Images
```bash
sudo mount -o ro,loop path/to/disk.img /mnt/mountpoint
```

- `ro`: Read-only mount (preserves evidence).
- `loop`: Enables mounting raw image files.

## File System Analysis with SleuthKit (TSK)
Install TSK via:

```bash
sudo apt install sleuthkit
```

TSK works with various file systems like `ext2/3/4`, `XFS`, `FAT`, `NTFS`.

###  Useful Commands

| Command | Description |
|--------|-------------|
| `fsstat <image>` | View metadata & layout of the file system (FS type, mount history, etc.) |
| `fls -rdp <image>` | Recursively list directories and recover deleted entries (`-r` = recursive, `-d` = deleted, `-p` = full paths) |
| `istat <image> <inode>` | Show detailed metadata of a specific file |
| `icat <image> <inode>` | Extract content of a file by its inode |
| `jls <image>` | View journal entries (for `ext3/4`) |
| `jcat <image> <block>` | Extract specific journal block content |
| `tsk_recover <image> <output_dir>` | Recover files from a file system (especially helpful for XFS) |

> Tip: Use `fls` to identify inodes and then use `istat` or `icat` to inspect or extract them.

## Recovery Tools

### TestDisk

Use **TestDisk** to:
- Recover lost/deleted partitions.
- Repair corrupted partition tables.
- Restore boot sectors.

```bash
sudo testdisk
```

Follow the guided interface:
1. Select your disk image.
2. Choose the correct partition table type.
3. Analyze → Quick Search → Recover.

### PhotoRec

Use **PhotoRec** when:
- File system is severely corrupted.
- You need to recover files based on file signatures.

```bash
sudo photorec
```

Steps:
1. Select the disk or image.
2. Choose the partition.
3. Set file system type (`Other` for FAT/NTFS, `ext2/ext3` for Linux).
4. Choose scan area: `Free` (unallocated) or `Whole`.
5. Set recovery destination (⚠️ never recover to same disk/image).

> To verify recovered files with virustotal via hash:

```bash
find . -type f -exec shasum {} \; | grep -i <hash>
```

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

## Evidence of Persistence 

  - init.d -> it will run while startup/booting
  - systemd service -> add malware as a service 
  - systemd timers -> timer to trigger a service. 
  > Note: Also service unit can be specified in timer "Unit"
  - Cron Jobs 
    - configuration for `contrab -l` -> /var/spool/cron/crontabs
    - crons saved in `/etc/cron`. (daily/hourly/monthly/weekly)
  - ssh keys (add pk in `/home/user/.ssh/authorized_keys`) directory
    - .ssh (chmod 700) / authorized_keys (600)
  - Bash configuration Files 
    - per User 
      - `/home/user/bashrc`
      - `/home/user/bash_profile`
      - `/home/user/.bash_login`
      - `/home/user/.bash_logout `
    - per system-wide
      - `/etc/bash.bashrc`
      - `/etc/profile`
      - `/etc/profile.d/*`
      - editing rc scripts `/etc/rc.local`
      - udev rules which responsible for hardware actions  `/etc/udev/rules.d`
      - xdg - only in user interface xdg autostart -> (`/etc/xdg/autostart`, `/home/user/.config/autostart`)
      - Network Manager script - run script based on network events - `/etc/NetworkManager/dispatcher.d` 
      - User creation/Modification 
      - Sudoers (add user to sudoers) `/etc/sudoers`


## Tips & Extra Resources 

###  Mounting Disk image vmdk for linux DFIR 
  - need to instal lvm 
    `apt install lvm2`
  - creating loop devce 
  ```bash 
    sudo losetup -f -P <diskImage.vmdk>
    sudo pvscan --cache
    sudo vgchange -ay
    sudo vgdisplay
    ```
  - mounting the image 
    ```bash
    sudo mount -o ro /dev/mapper/<vgName> <mountpoint>
    ```

### Creating a timeline 

#### create file timline 

 ```bash
 fls -r -m "/" <LocationdForImageTriage> <timeline.body>
mactime -b <filename-timline.body> -d -y > file_timeline.csv
```
#### create supertimeline
 - Craeting based on UAC via `log2timeline`, then all events could be done with `psteal` as follow
    ```bash
    log2timeline.py --storage-file <plasoOutputFIle> <uacFileLcation.zip.tar>
    psteal --storage-file <plasOutputFile> <DD image>
    ```
#### filtering timeline 

 - Filtering timeline based on date
 ```bash
   Log2timeline.py --storage-file <plasOutputFile> <DD image>
   psort -o dynamic -w <OutputCSVName> <PlasoStorageFile> "data > '2023-12-31 23:59:59' AND data < '2023-04-01 23:59:59'" 
   ```
 - Exporting files based on names, extension or signature 
 ```bash
image_export.py --signature list
image_export_py --names <name> <location of DD raw>
image_export.py --extension docx
----------
image_export.py --names ".bash_history" --write /tmp/exported_files --source /evidence/disk.dd
image_export.py --signature pdf,elf --write /tmp/exported_files --source /evidence/disk.dd
image_export.py --extension docx --write /tmp/exported_docx --source /evidence/disk.dd
image_export.py --extension docx --names "resume.docx" --write /tmp/resumes --source /images/host01.dd
 ```

## Memory analysis with voltatility 
 - Install Volatility 
 ```bash
 apt install -y python3-pip python3-pefile python3-yara git 
 pip3 install pycrypto
 git clone https://github.com/volatilityfoundation/volatility3.git 
 cd volatility3 ; python4 vol.py -h | more 
 ```
 - you need to get which Linux kernel is used, to install debsym to create conf.json
 ```bash
 python3 vol.py -f <memoryDump.lime> banners`
apt install ubuntu-dbgsym-keyring` 
apt install linux-kerenel-package-dbgsym`
ll /usr/lib/debug/boot`
./dwarf2json linux -elf /usr/lib/debug/boot/<nameOfInstalled_Linkxkern_dbg-sym> -> output.json`
mv output.json <volitaility/symbols
```
 - run plugins
    `vol.py -f <memDump.lime> <plugin>`
> Note: while installing you could have issue with python, you might need to create your own venv
```bash
python3 -m venv myenv
source myenv/bin/activate
```
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