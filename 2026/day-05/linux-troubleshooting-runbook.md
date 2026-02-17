# 🛠 Linux Troubleshooting Runbook  
## Day 05 – 90 Days DevOps Challenge

> Focused drill: CPU, Memory, Disk, Network, Logs

---

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

## ✅ Conclusion

- Manual execution failed (expected without elevated privileges).
- `systemd`-managed Nginx is healthy.
