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

## Memory Acquisition Tools

Memory acquisition is the first critical step in memory forensics. Below are some widely used tools and their usage guidance:

| Tool                | Description                                                                                   | How to Use                                                                 |
|---------------------|-----------------------------------------------------------------------------------------------|----------------------------------------------------------------------------|
| **FTK Imager**      | GUI tool to capture memory and disk images.                                                   | Open the tool → Select "Capture Memory" → Choose output location → Acquire |
| **Velociraptor/winpmem** | Lightweight memory acquisition with live analysis capabilities.                         | `winpmem.exe --output memory.raw` or integrate with Velociraptor live      |
| **Magnet RAM Capture** | Free tool by Magnet Forensics to capture physical memory.                                 | Launch → Accept license → Click "Capture" to save the image                |
| **MemProcFS**       | Mounts memory image as a live filesystem for analysis.                                        | `MemProcFS.exe -device <mem> -Forensic 1 -license-accept-elastic-license-2.0` |
| **Belkasoft RAM Capture** | Simple GUI-based RAM imaging.                                                         | Open tool → Capture memory to file                                         |

---

### Volatility CheatSheet

#### OS Information

##### imageinfo

**Volatility 2**:
```bash
# Windows
vol.py -f "/path/to/file" imageinfo
vol.py -f "/path/to/file" kdbgscan
# Linux 
vol.py -f "/path/to/file" banner
```

**Volatility 3**:
```bash
# Windows
vol.py -f "/path/to/file" windows.info
# Linux
vol.py -f "/path/to/file" banner
```

**Output differences**:
- **Volatility 2**: Additional info via `kdbgscan` if profile detection fails.
- **Volatility 3**: Includes x32/x64, OS versions, kdbg info.

Note: Volatility 3 is significantly faster for all commands.

---
#### Configurating Linux Profile in Ubuntu

##### Quick & Easy way

> This way is only supported if the linux varaint is popular 

```bash 
python3 vol.py --remote-isf-url 'https://github.com/Abyss-W4tcher/volatility3-symbols/raw/master/banners/banners.json' -f <memory_dump> <pluginName>
# Example 
vol3 --remote-isf-url 'https://github.com/Abyss-W4tcher/volatility3-symbols/raw/master/banners/banners.json' -f ubuntu.20211208.mem linux.pstree
# Note: After first run you can directly run the plugins 
```

##### Manual Configuration

```bash
# Grap the Linux Kernenl version
vol3 -f /pat/image.raw banners
# Update ddebs.list with ddebs repos
cat <<EOF | sudo tee /etc/apt/sources.list.d/ddebs.list
deb http://ddebs.ubuntu.com $(lsb_release -cs) main restricted universe multiverse
deb http://ddebs.ubuntu.com $(lsb_release -cs)-updates main restricted universe multiverse
deb http://ddebs.ubuntu.com $(lsb_release -cs)-proposed main restricted universe multiverse
EOF

# Accept the key of ddebs for ubuntu
curl -fsSL http://ddebs.ubuntu.com/dbgsym-release-key.asc | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/ddebs.gpg > /dev/null
# Update to make sure ddebs repos working
sudo apt update
# install dbgsym-keyring
sudo apt install ubuntu-dbgsym-keyring
#linux-image-5.4.0-1059-azure , linux-image-5.4.0-1059-azure-dbgsym
sudo apt install <linuxImage>-dbgsym
# Some time the *-dbgsym package have issues and can not be installed via package manager like apt, so you can install it manually via 
# https://launchpad.net/~canonical-kernel-team/+archive/ubuntu/ppa/+build/22073719
# https://launchpad.net/ubuntu/focal/amd64/linux-image-unsigned-5.4.0-1059-azure-dbgsym/5.4.0-1059.62
# https://launchpad.net/ubuntu/bionic/amd64/linux-image-unsigned-5.4.0-1059-azure-dbgsym

-----
# install dwarf2json
git clone https://github.com/volatilityfoundation/dwarf2json.git
cd dwarf2json
sudo apt install golang-go
go build
./dwarf2json linux --elf /usr/lib/debug/boot/linux-image-5.4.0-1059-azure > output.json
mv output.json volatility3/symbols
```


#### Process Information

##### pslist

**Volatility 2**:
```bash
vol.py -f "/path/to/file" --profile <profile> pslist
vol.py -f "/path/to/file" --profile <profile> psscan
vol.py -f "/path/to/file" --profile <profile> pstree
vol.py -f "/path/to/file" --profile <profile> psxview
```

**Volatility 3**:
```bash
vol.py -f "/path/to/file" windows.pslist
vol.py -f "/path/to/file" windows.psscan
vol.py -f "/path/to/file" windows.pstree
```

**Output differences**:
- **Volatility 2**: Includes psxview for cross-checks.
- **Volatility 3**: No direct psxview equivalent.

#### procdump

**Volatility 2**:
```bash
vol.py -f "/path/to/file" --profile <profile> procdump -p <PID> --dump-dir="/path/to/dir"
```

**Volatility 3**:
```bash
vol.py -f "/path/to/file" -o "/path/to/dir" windows.dumpfiles --pid <PID>
```

**Output differences**:
- **Volatility 2**: Dumps specified PID or all.
- **Volatility 3**: Dumps exe and related DLLs.

##### memdump

**Volatility 2**:
```bash
vol.py -f "/path/to/file" --profile <profile> memdump -p <PID> --dump-dir="/path/to/dir"
```

**Volatility 3**:
```bash
vol.py -f "/path/to/file" -o "/path/to/dir" windows.memmap --dump --pid <PID>
```

##### handles

**Volatility 2**:
```bash
vol.py -f "/path/to/file" --profile <profile> handles -p <PID>
```

**Volatility 3**:
```bash
vol.py -f "/path/to/file" windows.handles --pid <PID>
```

**Output differences**:
- Vol2: Offset, PID, handle, access, type, details
- Vol3: PID, process, offset, handlevalue, type, access, name

##### dlls

**Volatility 2**:
```bash
vol.py -f "/path/to/file" --profile <profile> dlllist -p <PID>
```

**Volatility 3**:
```bash
vol.py -f "/path/to/file" windows.dlllist --pid <PID>
```

##### cmdline

**Volatility 2**:
```bash
vol.py -f "/path/to/file" --profile <profile> cmdline
vol.py -f "/path/to/file" --profile <profile> cmdscan
vol.py -f "/path/to/file" --profile <profile> consoles
```

**Volatility 3**:
```bash
vol.py -f "/path/to/file" windows.cmdline
```

---

#### Network Information

##### netscan

**Volatility 2**:
```bash
vol.py -f "/path/to/file" --profile <profile> netscan
vol.py -f "/path/to/file" --profile <profile> netstat

# XP/2003 Specific
vol.py -f "/path/to/file" --profile <profile> connscan
vol.py -f "/path/to/file" --profile <profile> connections
vol.py -f "/path/to/file" --profile <profile> sockscan
vol.py -f "/path/to/file" --profile <profile> sockets
```

**Volatility 3**:
```bash
vol.py -f "/path/to/file" windows.netscan
vol.py -f "/path/to/file" windows.netstat
```

> XP/2003 plugins are deprecated in Vol3

---

#### Registry

##### hivelist

**Volatility 2**:
```bash
vol.py -f "/path/to/file" --profile <profile> hivescan
vol.py -f "/path/to/file" --profile <profile> hivelist
```

**Volatility 3**:
```bash
vol.py -f "/path/to/file" windows.registry.hivescan
vol.py -f "/path/to/file" windows.registry.hivelist
```

##### printkey

**Volatility 2**:
```bash
vol.py -f "/path/to/file" --profile <profile> printkey
vol.py -f "/path/to/file" --profile <profile> printkey -K "Software\Microsoft\Windows\CurrentVersion"
```

**Volatility 3**:
```bash
vol.py -f "/path/to/file" windows.registry.printkey
vol.py -f "/path/to/file" windows.registry.printkey --key "Software\Microsoft\Windows\CurrentVersion"
```

##### hivedump

**Volatility 2**:
```bash
vol.py -f "/path/to/file" --profile hivedump -o <offset>
```

**Volatility 3**:
> Not directly supported; may use filedump by offset.

---

#### Files

##### filescan

**Volatility 2**:
```bash
vol.py -f "/path/to/file" --profile <profile> filescan
```

**Volatility 3**:
```bash
vol.py -f "/path/to/file" windows.filescan
```

##### filedump

**Volatility 2**:
```bash
vol.py -f "/path/to/file" --profile <profile> dumpfiles --dump-dir="/path/to/dir"
vol.py -f "/path/to/file" --profile <profile> dumpfiles --dump-dir="/path/to/dir" -Q <offset>
vol.py -f "/path/to/file" --profile <profile> dumpfiles --dump-dir="/path/to/dir" -p <PID>
```

**Volatility 3**:
```bash
vol.py -f "/path/to/file" -o "/path/to/dir" windows.dumpfiles
vol.py -f "/path/to/file" -o "/path/to/dir" windows.dumpfiles --virtaddr <offset>
vol.py -f "/path/to/file" -o "/path/to/dir" windows.dumpfiles --physaddr <offset>
```

---

#### Miscellaneous

##### malfind

**Volatility 2**:
```bash
vol.py -f "/path/to/file" --profile <profile> malfind
```

**Volatility 3**:
```bash
vol.py -f "/path/to/file" windows.malfind
```

##### yarascan

**Volatility 2**:
```bash
vol.py -f "/path/to/file" yarascan -y "/path/to/file.yar"
```

**Volatility 3**:
```bash
vol.py -f "/path/to/file" windows.vadyarascan --yara-rules <string>
vol.py -f "/path/to/file" windows.vadyarascan --yara-file "/path/to/file.yar"
vol.py -f "/path/to/file" yarascan.yarascan --yara-file "/path/to/file.yar"
```

#### Practice

- **Art of Memory Forensics**: [memoryanalysis.net/amf](https://www.memoryanalysis.net/amf)
- Practice image: [Download](https://www.sendspace.com/pro/dl/yqjezf)
- Extended guide: [HackTricks Volatility Examples](https://book.hacktricks.xyz/forensics/volatility-examples)

---


## Extra Volatility Modules

| Plugin             | Purpose                                                                                  | Volatility Version |
|--------------------|------------------------------------------------------------------------------------------|--------------------|
| `ldrmodules`       | Lists loaded DLLs and their state (InLoad, InInit, InMemory). Helps detect injected DLLs | Vol2 / Vol3        |
| `malfind`          | Detects injected or malicious memory pages using characteristics like PAGE_EXECUTE_READ | Vol2 / Vol3        |
| `hollowfind`       | Detects process hollowing—replaced code in legitimate processes                          | Vol2 only          |
| `apihooks`         | Detects inline and IAT API hooks                                                         | Vol2 only          |
| `ssdt`             | Checks for System Service Descriptor Table (SSDT) hooking                                | Vol2 / Vol3        |
| `modules`, `modscan` | Detect loaded kernel modules and find hidden/unlinked drivers                         | Vol2 / Vol3        |
| `dumpfiles`        | Extracts files from memory (EXEs, DLLs, images)                                          | Vol2 / Vol3        |
| `procdump`         | Dumps entire process memory (requires `pslist` or `psscan`)                              | Vol2 / Vol3        |
| `dlldump`          | Dumps in-memory DLLs                                                                      | Vol2 only          |
| `moddump`          | Extracts kernel drivers from memory                                                      | Vol2 only          |
| `dlllist --dump`   | Dumps DLLs listed from `dlllist`                                                         | Vol3 only          |
| `modules --dump`   | Dumps kernel drivers                                                                      | Vol3 only          |

➡️ After dumping: Use **Yara**, **AV scanning**, **VirusTotal**, or **Reverse Engineering**.

---

## Memory Region Dumping

### Volatility 2

- `memdump`: Dumps all memory of a process into a single file.
- `vaddump`: Dumps VAD memory regions into separate files.
- `memmap`: Maps virtual addresses to physical memory.

### Volatility 3
- `memmap --dump`: Dumps memory regions via mapped virtual memory.

---

## Malware Memory Scanning

### Volatility 2
- `yarascan`: Scans memory using custom Yara rules.

### Volatility 3
- `yarascan.YaraScan`: Primary Yara scanning plugin.
- `windows.vadyarascan`: Scans Virtual Address Descriptor memory regions.

---
## Windgb using Volshell

Volshell is a memory shell debugger-like environment for advanced memory inspection.

### Volatility 2 Commands

| Command   | Purpose                                                        |
|-----------|----------------------------------------------------------------|
| `help()`  | Displays all available volshell commands                       |
| `hh()`    | Hex dump of memory starting at a virtual address               |
| `lp()`    | Lists loaded processes and their base addresses                |
| `dt()`    | Display structure types (from symbol tables)                   |
| `cc()`    | Call convention simulation                                     |
| `sc()`    | Search memory for a pattern                                    |

### Volatility 3 Usage

```bash
python volshell.py -f <memory.raw> -w
```
Then use similar commands (`help()`, `ps()`, `dt()`, `db()`):
- `ps()`: List running processes
- `db(addr)`: Dump bytes at address
- `dt(struct, addr)`: Interpret data at address using a known structure
- `cp(addr)`: Set current pointer

---

## Using MemProcFS

MemProcFS allows mounting memory images as a virtual filesystem.

### Usage
```bash
MemprocFS.exe -device memory.raw -Forensic 1 -license-accept-elastic-license-2.0
```

### Filesystem Hierarchy

| Path                    | Purpose                                                  |
|-------------------------|----------------------------------------------------------|
| `/processes/`           | List of processes with PID and metadata                  |
| `/modules/`             | Kernel modules                                           |
| `/handles/`             | Process and object handles                               |
| `/files/`               | File objects extracted from memory                       |
| `/registry/`            | Reconstructed Windows registry hives                    |
| `/dlls/`, `/drivers/`   | Memory-dumped DLLs and drivers                           |
| `/strings/`             | Extracted strings from various memory regions            |
| `/malfind/`             | Suspicious memory pages (like Volatility’s malfind)      |

You can `cd` into these directories or copy them out like regular files.
---

##  Converting Hibernation Files

- Use `imagecopy` in Volatility 2 to convert `.hiberfil.sys`
- Magnet’s **DumpIt** tool can also extract memory from hibernation

---
---

## Memory Crash Dump Analysis with WinDbg

When no `.dmp` is available, use **MemProcFS** to generate one and load into **WinDbg**.

### Setup Steps
1. Launch WinDbg and open the `.dmp` file.
2. Load symbols (if not auto-configured).
3. Use extensions and commands to inspect.

### Useful WinDbg Commands

| Command               | Purpose                                                              |
|------------------------|----------------------------------------------------------------------|
| `!analyze -v`          | Full dump analysis, often finds root cause                          |
| `dx`                   | Explore Data Model (`sessions`, `state`, `Utility`, etc.)           |
| `!process 0 0`         | List all processes                                                   |
| `!process <addr>`      | Detailed info about a specific process                              |
| `.process /r /p <addr>`| Set context to process (read and privileged mode)                   |
| `!peb`                 | Display Process Environment Block info                              |
| `!vad`                 | View Virtual Address Descriptors                                     |
| `!handle 0 f file`     | Show all file handles                                                |
| `db <addr>`            | Hex dump of memory                                                   |


### Useful Commands
- `!help`, `.hh <command>`
- `!peb`, `!vad`, `!handle 0 f file`, `!handle 0 f key`
- `db <memory address>`: View raw memory



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