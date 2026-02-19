# 🛠 Linux Troubleshooting Runbook  
## Day 05 – 90 Days DevOps Challenge

> Focused drill: CPU, Memory, Disk, Network, Logs



## 🎯 Target Service

- **Service:** `nginx`
- **Reason:** Public-facing web server; critical for availability.

---

## 1️⃣ Environment Basics

### 🔹 Kernel & System Info

```bash
uname -a
```

**Output:**

```text
Linux ip-172-31-5-198 6.14.0-1018-aws #18~24.04.1-Ubuntu SMP Mon Nov 24 19:46:27 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
```

**Observation:**

- Running on AWS.
- Kernel: `6.14.0` (Ubuntu build).
- Architecture: `x86_64`.

```bash
lsb_release -a
```

**Output:**

```text
Distributor ID: Ubuntu
Description:    Ubuntu 24.04.3 LTS
Release:        24.04
Codename:       noble
```

**Observation:**

- OS: Ubuntu `24.04.3 LTS` (Noble).
- Stable LTS environment.

---

## 2️⃣ Filesystem Sanity Check

### 🔹 Create Test Directory

```bash
mkdir /tmp/runbook-demo
cp /etc/hosts /tmp/runbook-demo/hosts-copy
ls -l /tmp/runbook-demo
```

**Output:**

```text
-rw-r--r-- 1 ubuntu ubuntu 221 Feb 17 14:58 hosts-copy
```

**Observation:**

- Directory created successfully.
- File copied without errors.
- Permissions are normal.
- Filesystem is writable and functioning correctly.

---

## 3️⃣ Service Behavior Check

### 🔹 Manual Start Attempt

```bash
nginx
```

**Output:**

```text
open() "/var/log/nginx/error.log" failed (13: Permission denied)
```

**Observation:**

- Direct CLI start failed due to a permission issue.
- Nginx requires proper privileges when started manually.
- Indicates log directory ownership/permission restrictions.

### 🔹 Check Service via systemd

```bash
systemctl status nginx
```

**Output (summary):**

```text
Active: active (running)
Main PID: 13078
Memory: 2.9M
```

**Observation:**

- Service is running normally.
- Two worker processes active.
- Very low memory usage.
- Started ~7 hours ago.
- No restart loops detected.

---

## 4️⃣ Snapshot: CPU & Memory

### 🔹 Real-Time System Load

```bash
top
```

**Output (summary):**

```text
load average: 0.00, 0.00, 0.00
Tasks: 114 total, 1 running, 0 zombie
%Cpu(s): 100.0 id
MiB Mem : 914.2 total, 366.1 used, 192.3 free, 525.9 buff/cache
MiB Swap: 0.0 total, 0.0 used
```

**Observation:**

- Load average is `0.00` → no CPU pressure.
- CPU is `100%` idle → system completely relaxed.
- No zombie processes.
- Memory usage is moderate and healthy.
- No swap usage → no memory pressure.

```bash
top -p 13078
```

**Output:**

```text
Tasks: 1 total, 0 running, 1 sleeping, 0 zombie
%Cpu(s): 99.8 id
MiB Mem: 914.2 total, 405.1 used, 212.5 free, 469.7 buff/cache
PID 13078 nginx 0.0% CPU 0.2% MEM 
```

**Observation:**

- PID 13078 is `nginx`.
- Only nginx process being monitored.
- CPU usage: 0.0% → No load on nginx.
- Memory usage: 0.2% (~2MB) → Very lightweight.
- Process state: Sleeping (normal when idle).
- No resource spike detected.
- System remains ~99% idle.

### 🔹 Process Inspection

```bash
ps -o pid,comm
```

**Output:**

```text
21270 bash
21353 ps
```

**Observation:**

- Active interactive shell (`bash`).
- `ps` visible as expected (self-invoked).
- No abnormal or high-load processes detected.

```bash
ps -p 1
```

**Output:**

```text
PID TTY      TIME CMD
1   ?        00:00:11 systemd
```

**Observation:**

- PID 1 is `systemd`.
- Init system running normally.

```bash
ps -p 2
```

**Output:**

```text
PID TTY      TIME CMD
2   ?        00:00:00 kthreadd
```

**Observation:**

- Kernel thread manager is active.
- Normal background system operation.

### 🔹 Memory Overview

```bash
free -h
```

**Output:**

```text
Mem: 914Mi total, 366Mi used, 192Mi free, 525Mi buff/cache, 548Mi available
Swap: 0B total, 0B used
```

**Observation:**

- ~`366Mi` RAM in use (normal baseline).
- `0` swap usage.
- `548Mi` available memory.
- No memory exhaustion signs.

---

## 5️⃣ Disk & IO Snapshot

### 🔹 VM Statistics

```bash
vmstat
```

**Output (summary):**

```text
r=2  b=0  swpd=0  si=0  so=0  wa=0
```

**Observation:**

- No swapping activity.
- No blocked processes.
- IO wait (`wa`) = `0`.
- CPU remains idle.
- System not under IO pressure.

### 🔹 Disk IO Statistics

```bash
iostat
```

**Output (summary):**

```text
avg-cpu: 99.90% idle
nvme0n1: minimal read/write activity
```

**Observation:**

- CPU idle around `99%`.
- Very low disk read/write activity.
- No disk bottlenecks detected.
- Underlying storage appears healthy.

### 🔹 Disk Usage

```bash
df -h
```

**Output (summary):**

```text
/dev/root  19G  2.3G  17G  13%  /
```

**Observation:**

- Root filesystem usage is only `13%`.
- ~`17GB` available space.
- No partitions above `80%`.
- No immediate disk space risk.

### 🔹 Log Directory Size

```bash
du -sh /var/log
```

**Output:**

```text
21M /var/log
```

**Observation:**

- Log directory size is only `21MB`.
- No abnormal log growth.
- Permission errors when run without `sudo` are expected in some paths.
- No disk pressure from logs.

### 🔹 Real-Time IO Activity

```bash
dstat
```

**Output (summary):**

```text
CPU idle ~99%
Minimal disk read/write
Very low network traffic
No paging activity
```

**Observation:**

- CPU mostly idle.
- No disk spikes.
- No paging (no swap usage).
- System not under IO pressure.
- Healthy baseline behavior.

---

## 6️⃣ Network Snapshot

### 🔹 Listening Ports

```bash
ss -tulpn
```

**Output (summary):**

```text
LISTEN 0.0.0.0:22
LISTEN 0.0.0.0:80
```

**Observation:**

- SSH listening on port `22`.
- Nginx listening on port `80` (IPv4 and IPv6).
- No unexpected services exposed.
- No port conflicts detected.

### 🔹 External Connectivity Check

```bash
curl -I google.com
```

**Observation:**

- HTTP `301` response received.
- Outbound internet connectivity working.
- DNS resolution functioning correctly.

```bash
ping google.com
```

**Observation:**

- ICMP replies successful.
- Average latency ~`3ms`.
- Network stable and responsive.

---

## 7️⃣ Logs Reviewed

### 🔹 Nginx Service Logs

```bash
journalctl -u nginx
```

**Output (summary):**

```text
Service started successfully.
Service stopped and restarted once.
No crash errors.
```

**Observation:**

- Nginx started normally.
- One clean stop/start sequence observed.
- No failure loops.
- No critical error messages.

### 🔹 Application Log (Zookeeper Example)

```bash
tail -n 10 zookeeper.log
```

**Observation:**

- Informational session activity logs.
- No fatal errors.
- Logs appear historical (2015 timestamps).
- No active crash indicators.

---

## 8️⃣ Quick Findings

- `nginx` service is active and stable under `systemd`.
- No configuration errors detected (`nginx -T` successful).
- CPU usage near `0%`, load average `0.00` → no compute pressure.
- Memory usage within a safe baseline; no swap activity.
- Disk usage only `13%` → no storage risk.
- Log directory is small (`21MB`) → no abnormal growth.
- No errors in `nginx error.log` (`0 bytes`).
- `strace` confirms master process is idle and waiting for signals.
- Network connectivity is stable (DNS + outbound traffic working).
- No abnormal IO, CPU spikes, or blocked processes observed.

**Overall System State:** Healthy baseline.
No signs of resource exhaustion, service instability, or misconfiguration.

---

## 🚨 If This Worsens (Next Steps)

### 1️⃣ Graceful Restart Strategy (if Nginx becomes unresponsive)

```bash
sudo systemctl restart nginx
sudo systemctl status nginx
```

- Verify clean restart.
- Confirm new PID and active state.
- Re-check CPU and logs after restart.

### 2️⃣ Increase Log Visibility (if errors begin appearing)

```bash
journalctl -u nginx -n 200
sudo tail -n 100 /var/log/nginx/error.log
```

- Look for `5xx` errors, upstream timeouts, and permission failures.
- Monitor log growth rate.
- Confirm correct `error_log` path via `nginx -T`.

### 3️⃣ Deep Process-Level Inspection (if high CPU or hang occurs)

```bash
top -p <nginx_pid>
sudo strace -p <nginx_pid>
sudo lsof -p <nginx_pid>
```

- Identify CPU spikes or memory leaks.
- Check for blocking I/O calls.
- Inspect open file descriptors.
- Investigate worker processes individually if needed.

