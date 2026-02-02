# 🐧 Linux Commands Cheat Sheet  
**Day 03 – Linux Commands Practice**  
> Built for real troubleshooting, not exams.

This is my personal Linux command toolkit — short, sharp, and battle-tested.  
Designed to be scanned under pressure.

---

## 📁 File System & Disk

| Command | Usage | Why it matters |
|------|------|---------------|
| `ls -lah` | List files with permissions & sizes | Quick directory inspection |
| `pwd` | Print current directory | Know where you are |
| `cd /path` | Change directory | Basic navigation |
| `du -sh *` | Show size of files/folders | Find disk hogs |
| `df -h` | Disk usage by filesystem | Detect full disks |
| `stat file` | Detailed file info | Permissions & timestamps |
| `find /path -name file` | Search files | Locate configs/logs |
| `chmod 755 file` | Change permissions | Fix access issues |
| `chown user:group file` | Change ownership | Service permission fixes |
| `mount` | Show mounted filesystems | Debug storage issues |

---

## ⚙️ Process & System Management

| Command | Usage | Why it matters |
|------|------|---------------|
| `ps aux` | List all running processes | System overview |
| `top` | Live process monitoring | CPU/RAM spikes |
| `htop` | Better `top` (if installed) | Easier visibility |
| `uptime` | System run time & load | Health check |
| `free -h` | Memory usage | Detect memory pressure |
| `vmstat` | CPU/memory stats | Performance diagnosis |
| `kill PID` | Stop a process | Kill misbehaving apps |
| `kill -9 PID` | Force kill | Last resort |
| `nice / renice` | Adjust priority | Resource control |
| `systemctl status service` | Check service state | Production debugging |

---

## 🌐 Networking & Connectivity

| Command | Usage | Why it matters |
|------|------|---------------|
| `ip addr` | Shows addresses assigned to all network interfaces | Interface debugging |
| `ifconfig` | Display all network interfaces with IP addresses | Interface debugging |
| `ping host` | Check connectivity | Is it reachable? |
| `ss -tulnp` | Open ports & services | What’s listening |
| `netstat -nutlp` | Legacy port check | Old systems |
| `curl url` | Test HTTP endpoint | API health |
| `dig domain` | DNS lookup | DNS resolution issues |
| `traceroute host` | Path tracing | Network latency |
| `nc -zv host port` | Port connectivity test | Firewall checks |
| `whois domain` | Display DNS info from domain | Identifies network ownership/accountability |
| `host domain` | Display DNS IP address for the domain | Maps names to IPs |
| `wget link` | Download from location link | Downloads files via CLI |
| `ufw allow port` | Allow traffic from specific port through a firewall | Permits specific network traffic |
| `nmcli` | Command-line tool for network management | managing network in system |

---

## 📄 Logs & Inspection

| Command | Usage | Why it matters |
|------|------|---------------|
| `tail -f file` | output the last part of files | Real-time debugging |
| `less file` | Scroll logs safely | Large file handling |
| `head file` | First lines of file | Quick check |
| `grep "error" file` | Search logs | Find failures |
| `grep -i` | Case-insensitive search | Faster filtering |
| `awk '{print $1}'` | Column extraction | Log parsing |
| `cut -d':' -f1` | Split fields | Data inspection |
| `wc -l file` | Line count | Log volume check |

---

## 🧠 Operator Notes

- Prefer **reading logs** before restarting services  
- Disk full issues cause more outages than bugs  
- Always check **network + DNS** before blaming the app  
- Simple commands solve most production problems  
- At last RTFM! for more info

---

## 🎯 Why This Cheat Sheet Exists

In DevOps, speed matters.

The faster you can:
- inspect processes
- verify networking
- read logs  

the faster you:
- restore service
- reduce downtime
- gain trust

This file grows with experience.  
Tools change. **Linux fundamentals don’t.**

---
