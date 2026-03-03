# Memory Forensics Cheat Sheet - Linux & Windows

Quick reference for memory acquisition and analysis on Linux and Windows, helping SOC analysts investigate and respond to incidents efficiently.

## Memory Acquisition

| OS      | Tool / Command                                                |
| ------- | ------------------------------------------------------------- |
| Linux   | `LiME` -> `insmod lime.ko "path=/tmp/memdump.mem format=raw"` |
| Windows | `FTK Imager`, `DumpIt`, `Belkasoft RAM Capture`               |
| macOS   | `OSXPmem`                                                     |

---

### Verify Memory Dump

```sh
sha256sum memdump.mem
md5sum memdump.mem
```

### Linux Live Memory Analysis

| Focus                | Command / Tool                     |
| -------------------- | ---------------------------------- |
| Processes            | `ps aux`, `top`                    |
| Open Files / Sockets | `lsof -i`, `ss -tulpn`             |
| Network Activity     | `ss -s` or `tcpdump -i eth0`       |
| User Sessions        | `w`, `last`                        |
| Suspicious Exec      | `grep -r "malware" /proc/*/exe`    |
| Scheduled Tasks      | `crontab -l`, `cat /etc/crontab`   |
| System Logs          | `journalctl -xe`                   |
| Memory Dump          | `gcore <PID>` or `/proc/<PID>/mem` |

---

## Windows Memory Analysis - Volatility 3

### Process Analysis

```sh
python3 vol.py -f memdump.mem windows.info.Info
python3 vol.py -f memdump.mem windows.pslist.PsList
python3 vol.py -f memdump.mem windows.psscan.PsScan
python3 vol.py -f memdump.mem windows.pstree.PsTree
```

### Process & Malware Analysis

| Plugin     | Purpose                       |
| ---------- | ----------------------------- |
| pslist     | List processes                |
| psscan     | Hidden / terminated processes |
| pstree     | Process tree                  |
| cmdline    | Command-line arguments        |
| dlllist    | Loaded DLLs                   |
| malfind    | Injected code / malware       |
| apihooks   | API hooks                     |
| ldrmodules | DLL injections                |
| handles    | Open files / registry handles |
| svcscan    | Services                      |
| driverscan | Kernel drivers                |

### Network & Privileges

```sh
vol.py -f memdump.mem windows.netscan.NetScan
vol.py -f memdump.mem windows.netstat.NetStat
vol.py -f memdump.mem windows.privileges.Privs
```

### Persistence / Autoruns

```sh
vol.py -f memdump.mem windows.autoruns.Autoruns
vol.py -f memdump.mem windows.registry.printkey --key "Software\Microsoft\Windows\CurrentVersion\Run"
```

### File & Timeline Analysis

```sh
vol.py -f memdump.mem windows.filescan.FileScan
vol.py -f memdump.mem timeliner
```

---

### Quick Linux Incident Commands

```sh
# Block suspicious IP
sudo iptables -A INPUT -s <IP> -j DROP

# Kill suspicious process
sudo kill -9 <PID>

# Search recently modified files
find /home -type f -mtime -7

# Check failed logins
grep "Failed password" /var/log/auth.log
```

---

### Quick Windows Incident Commands

```sh
# List failed logons
Get-WinEvent -LogName Security | Where-Object { $_.Id -eq 4625 }

# Kill suspicious process
Stop-Process -Id <PID> -Force

# Delete autorun key
Remove-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "<KeyName>"

# List open TCP connections
Get-NetTCPConnection
```

---

### Useful Resources

- [FTK Imager](https://www.exterro.com/digital-forensics-software/ftk-imager)

- [Volatility 3](https://github.com/volatilityfoundation/volatility3/releases/)

- [VirusTotal](https://www.virustotal.com/gui/home/search)

- [QuickHash](https://www.quickhash-gui.org/)
