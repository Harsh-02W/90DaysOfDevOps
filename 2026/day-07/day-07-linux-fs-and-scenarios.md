# Day 07 — Linux File System Hierarchy & Scenario-Based Practice

> **#90DaysOfDevOps** | Week 2 | Building the Foundation

---

## Table of Contents
- [Part 1: Linux File System Hierarchy](#part-1-linux-file-system-hierarchy)
- [Part 2: Scenario-Based Practice](#part-2-scenario-based-practice)
- [Key Takeaways](#key-takeaways)

---

## Part 1: Linux File System Hierarchy

> Think of the Linux file system as a tree. Everything starts from `/` (root) and branches out. Unlike Windows (`C:\`, `D:\`), Linux has **one unified tree** — even external drives, USBs, and network shares are mounted somewhere inside it.

```
/
├── home/        → Regular users live here
├── root/        → Root user's private home
├── etc/         → All configuration files
├── var/log/     → Logs, logs, logs — your best friend in prod
├── tmp/         → Temporary files (auto-cleared on reboot)
├── bin/         → Core system binaries
├── usr/bin/     → Installed user-facing programs
└── opt/         → Third-party / manually installed software
```

---

### Core Directories (Must Know)

#### `/` — Root
- **What it contains:** The absolute top of the Linux file system. Every single file and directory on the system lives under `/`.
- **Peek inside:** `ls -l /` → you'll see `bin`, `etc`, `home`, `var`, `usr`, `tmp`, etc.
- **I would use this when...** navigating from scratch or writing absolute paths in scripts/configs to avoid ambiguity.

---

#### `/home` — User Home Directories
- **What it contains:** A personal directory for each non-root user (e.g., `/home/john`, `/home/deploy`). Stores user files, shell configs (`.bashrc`, `.ssh/`), and personal data.
- **Peek inside:** `ls -l /home` → you'll see a folder per user account on the system.
- **I would use this when...** checking a developer's SSH keys, sourcing their `.env` files, or debugging permission issues on user-owned scripts.

---

#### `/root` — Root User's Home
- **What it contains:** The home directory exclusively for the `root` superuser. Separate from `/home` so root always has a safe space even if `/home` is unmounted.
- **Peek inside:** `ls -la /root` → typically shows `.bashrc`, `.bash_history`, `.ssh/`.
- **I would use this when...** logged in as root and checking what scripts or SSH keys the root user has configured.

---

#### `/etc` — Configuration Files
- **What it contains:** System-wide configuration files for almost every service and application — `nginx.conf`, `ssh/sshd_config`, `hosts`, `passwd`, `crontab`, and more.
- **Peek inside:** `ls -l /etc` → you'll see `hostname`, `hosts`, `cron.d/`, `ssh/`, `systemd/`.
- **I would use this when...** editing a service's config, troubleshooting SSH access, managing cron jobs, or setting environment variables system-wide.

```bash
# Quick example
cat /etc/hostname       # See the machine's hostname
cat /etc/hosts          # Check local DNS overrides
```

---

#### `/var/log` — Log Files ⭐ *Most Important for DevOps*
- **What it contains:** Runtime logs from the OS, services, and applications — `syslog`, `auth.log`, `nginx/access.log`, `dpkg.log`, etc. This is your **first stop** when something breaks.
- **Peek inside:** `ls -l /var/log` → you'll see `syslog`, `auth.log`, `kern.log`, service-specific folders.
- **I would use this when...** any service fails, there's a security incident, or a deployment goes wrong. *Logs don't lie.*

```bash
# Find the largest log files
du -sh /var/log/* 2>/dev/null | sort -h | tail -5

# Tail system logs live
tail -f /var/log/syslog
```

---

#### `/tmp` — Temporary Files
- **What it contains:** Short-lived temporary files created by applications and users. Cleared automatically on reboot (and sometimes periodically via `systemd-tmpfiles`).
- **Peek inside:** `ls -l /tmp` → random session files, socket files, build artifacts.
- **I would use this when...** downloading a quick script to test before moving it somewhere permanent, or writing automation that needs a safe scratch space.

> ⚠️ **Never store anything important here.** It *will* disappear.

---

### Additional Directories (Good to Know)

#### `/bin` — Essential System Binaries
- **What it contains:** Core binaries needed for the system to function even in single-user/recovery mode — `ls`, `cat`, `cp`, `bash`, `echo`, etc.
- **Peek inside:** `ls -l /bin` → on modern distros, this is often a symlink to `/usr/bin`.
- **I would use this when...** troubleshooting in minimal/recovery environments where `/usr` might not be mounted.

---

#### `/usr/bin` — User Command Binaries
- **What it contains:** The bulk of installed user-facing programs — `git`, `python3`, `curl`, `wget`, `docker`, `vim`, etc.
- **Peek inside:** `ls -l /usr/bin` → hundreds of executables from installed packages.
- **I would use this when...** verifying if a tool is installed (`which docker → /usr/bin/docker`) or checking binary versions.

```bash
which curl          # → /usr/bin/curl
ls -l /usr/bin/git  # Check if git is installed
```

---

#### `/opt` — Optional / Third-Party Applications
- **What it contains:** Manually installed or third-party software that doesn't follow standard Linux packaging — tools like `JetBrains IDEs`, `custom agents`, `Datadog`, or self-compiled apps often land here.
- **Peek inside:** `ls -l /opt` → vendor-named folders like `datadog-agent/`, `jdk-21/`, `mycompany/`.
- **I would use this when...** installing monitoring agents, custom runtimes, or any software shipped as a tarball rather than via `apt`/`yum`.

---

## Part 2: Scenario-Based Practice

> **Mindset:** Don't memorize commands. Understand the *flow* — observe → investigate → fix → verify. That's what separates a DevOps engineer from someone who just googles error messages.

---

### ✅ Solved Example — Checking if a Service is Running

**Scenario:** Is `nginx` running?

```bash
# Step 1: Check service status
systemctl status nginx
# Why: Gives you active/failed/stopped state + last few log lines instantly

# Step 2: List all services if not found
systemctl list-units --type=service
# Why: Confirms what services exist on this machine

# Step 3: Check if it's enabled on boot
systemctl is-enabled nginx
# Why: A service can be "active now" but won't survive a reboot if not enabled
```

**Learned:** Status first → logs next → boot config last.

---

### 🔴 Scenario 1 — Service Not Starting

**Situation:** Web app service `myapp` failed to start after a server reboot.

```bash
# Step 1: Check current state of the service
systemctl status myapp
# Why: The status output tells you if it's failed, inactive, or crashing.
#      Look for "Active: failed" and the exit code.

# Step 2: View recent logs for the service (last 50 lines)
journalctl -u myapp -n 50
# Why: The actual error (missing file, port conflict, bad config) will be here.
#      This is your most important step.

# Step 3: Check if the service is enabled to start on boot
systemctl is-enabled myapp
# Why: If it's "disabled", it won't auto-start on reboot — that explains the symptom.

# Step 4: Attempt to start it manually to see live errors
systemctl start myapp
# Why: Combined with Step 2 (run journalctl -f in another terminal), you see
#      the failure in real-time as it happens.

# Step 5 (Bonus): Check the service unit file for misconfigurations
systemctl cat myapp
# Why: Reveals the ExecStart path, User, WorkingDirectory — any typo here = failure.
```

**Flow:** `status` → `logs` → `enabled?` → `start manually` → `inspect unit file`

---

### 🟡 Scenario 2 — High CPU Usage

**Situation:** Manager says the app server is slow. You SSH in. What now?

```bash
# Step 1: Get a live view of CPU usage by process
top
# Why: Shows real-time CPU%, MEM%, PID sorted by consumption.
#      Press 'P' to sort by CPU, 'q' to quit.

# Step 2: Non-interactive snapshot (great for scripts/logging)
ps aux --sort=-%cpu | head -10
# Why: Lists top 10 CPU-consuming processes right now, no interactive mode needed.
#      Easier to parse in incident reports.

# Step 3: Note the PID of the offending process, then inspect it
ls -l /proc/<PID>/exe
# Why: Reveals the actual binary behind the PID — useful when process name is generic.

# Step 4: Check if it's a runaway thread or child process
ps -p <PID> -o pid,ppid,cmd,%cpu,%mem
# Why: Shows parent PID (PPID) — sometimes the child is symptomatic,
#      but the parent is the real culprit.

# Step 5 (Bonus): Check load average over time
uptime
# Why: "load average: 0.5, 2.1, 4.8" — rising numbers mean sustained pressure,
#      not just a spike. Helps you tell your manager "this has been bad for 15 mins".
```

**Flow:** `top` → identify PID → `ps` deep-dive → trace to parent → check load history

---

### 🔵 Scenario 3 — Finding Service Logs

**Situation:** A developer asks "Where are the Docker service logs?"

```bash
# Step 1: Confirm docker is a systemd-managed service
systemctl status docker
# Why: If it's managed by systemd, its logs flow into journald — not a flat file.

# Step 2: View the last 50 log lines for docker
journalctl -u docker -n 50
# Why: -u filters by unit (service name), -n limits output.
#      This is the primary log source for systemd services.

# Step 3: Follow logs in real-time (like tail -f)
journalctl -u docker -f
# Why: Essential when you're reproducing a bug live and need to watch events unfold.

# Step 4: If you need logs from a specific time window
journalctl -u docker --since "2024-01-15 10:00:00" --until "2024-01-15 11:00:00"
# Why: Incident happened at 10:30? Narrow down the noise. Critical for post-mortems.

# Step 5 (Bonus): Check container-level logs (not service logs)
docker logs <container_name> --tail 100
# Why: The developer probably wants app logs, not Docker daemon logs.
#      Clarify what they actually need before diving in.
```

**Flow:** Confirm it's `systemd-managed` → `journalctl -u` → use `-f` for live → scope by time if needed

> 💡 **Rule of thumb:** systemd service = `journalctl`. App writing its own log file = `/var/log/<appname>/`. Container = `docker logs`.

---

### 🟢 Scenario 4 — File Permission Issue (Script Won't Execute)

**Situation:** Running `./backup.sh` gives `Permission denied`.

```bash
# Step 1: Check current permissions
ls -l /home/user/backup.sh
# Why: You'll see something like -rw-r--r-- (no 'x' = not executable).
#      Read the output left to right: owner | group | others.

# Step 2: Add execute permission
chmod +x /home/user/backup.sh
# Why: +x adds execute permission for owner, group, and others.
#      Use chmod 750 if you want tighter control (owner=rwx, group=rx, others=none).

# Step 3: Verify the fix
ls -l /home/user/backup.sh
# Why: Confirm you now see -rwxr-xr-x before telling the developer it's fixed.

# Step 4: Run it
./backup.sh
# Why: Always verify the actual fix works end-to-end, not just "the command ran".

# Step 5 (Bonus): Check who owns the file
stat /home/user/backup.sh
# Why: If you're running the script as a different user, even with +x,
#      ownership and group membership matter. stat shows owner, group, and all times.
```

**Permission Cheatsheet:**
```
-rwxr-xr-x
 │││││││││└─ others: execute (x)
 ││││││││└── others: no write
 │││││││└─── others: read (r)
 ││││││└──── group: execute (x)
 │││││└───── group: no write
 ││││└────── group: read (r)
 │││└─────── owner: execute (x)
 ││└──────── owner: write (w)
 │└───────── owner: read (r)
 └────────── file type (- = file, d = directory)
```

---

## Key Takeaways

| Concept | Remember This |
|---|---|
| `/etc` | Config lives here. Break this, break the service. |
| `/var/log` | First place to look when anything fails. |
| `/tmp` | Scratch space. Gone on reboot. |
| `/opt` | Third-party tools that bypass the package manager. |
| `journalctl -u <service>` | systemd service logs. Use `-f` to follow live. |
| `systemctl status` | Always start here for service issues. |
| `chmod +x` | Script not running? Check execute permission first. |
| `ps aux --sort=-%cpu` | Quick non-interactive CPU culprit finder. |

---

> **Troubleshooting Flow (Universal):**
> ```
> Observe (what's broken?) → Investigate (logs/status) → Hypothesize → Fix → Verify
> ```
> Never skip the **Verify** step. "I think it's fixed" is not fixed.

---