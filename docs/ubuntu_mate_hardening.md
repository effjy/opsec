# 🔐 Ubuntu MATE 24.04.4 LTS Hardening Guide

[![Documentation](https://img.shields.io/badge/docs-opsec-blue.svg)](./)
[![License](https://img.shields.io/badge/license-CC%20BY--NC--SA%204.0-lightgrey.svg)](../LICENSE)
[![Ubuntu](https://img.shields.io/badge/Ubuntu-24.04.4-E95420?logo=ubuntu&logoColor=white)](https://ubuntu.com/)
[![Security](https://img.shields.io/badge/Security-Hardening-brightgreen.svg)](https://ubuntu.com/security)
[![CIS](https://img.shields.io/badge/CIS-Benchmark-blue.svg)](https://www.cisecurity.org/benchmark/ubuntu_linux/)

---

## 📋 Table of Contents

1. [🔑 Core Principles](#core-principles)
2. [🔄 System Updates & Maintenance](#system-updates--maintenance)
3. [👤 User Account Hardening](#user-account-hardening)
4. [🛡️ AppArmor & Kernel Security](#apparmor--kernel-security)
5. [🔥 Firewall Configuration (UFW/nftables)](#firewall-configuration-ufwnftables)
6. [🔐 SSH Security Hardening](#ssh-security-hardening)
7. [⚙️ Kernel Hardening (sysctl)](#kernel-hardening-sysctl)
8. [📁 Filesystem & Partition Security](#filesystem--partition-security)
9. [🦠 Malware & Rootkit Protection](#malware--rootkit-protection)
10. [🎯 Threat Modeling & Adversary Profiling](#threat-modeling--adversary-profiling)
11. [🔍 OSINT & Counter‑Surveillance Considerations](#osint--counter‑surveillance-considerations)
12. [✅ Operational Checklists](#operational-checklists)
13. [📚 Further Reading](#further-reading)

---

## 🔑 Core Principles

This guide follows industry‑standard hardening practices, including the **CIS (Center for Internet Security) benchmarks for Ubuntu 24.04 LTS**. Many steps can be automated using the **Ubuntu Security Guide (USG)** tool, which audits and remediates configurations.

> 💡 **Why Ubuntu MATE?** Ubuntu MATE 24.04 inherits a hardened Linux kernel 6.8 with advanced features:
> - AppArmor 4 with unprivileged user namespace restrictions
> - Binary hardening (PIE, stack canaries, FORTIFY_SOURCE)
> - Hardware‑assisted memory protection (Intel CET, AMD Shadow Stack)
> - Expanded Security Maintenance (ESM) for up to 12 years.

---

## 🔄 System Updates & Maintenance

### 1.1. Manual updates

```bash
sudo apt update && sudo apt full-upgrade -y
sudo apt autoremove --purge -y
```

### 1.2. Unattended security upgrades

```bash
# Install (if not present)
sudo apt install unattended-upgrades -y

# Enable automatic security updates
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

Verify configuration in `/etc/apt/apt.conf.d/50unattended-upgrades`:

```ini
Unattended-Upgrade::Allowed-Origins {
    "${distro_id}:${distro_codename}-security";
};
Unattended-Upgrade::Automatic-Reboot "false";
Unattended-Upgrade::Remove-Unused-Dependencies "true";
```

Enable automatic‑reboot only if acceptable.

### 1.3. Enable ESM (Ubuntu Pro)

```bash
sudo pro attach <your-token>   # Free for up to 5 machines
sudo pro enable esm-apps esm-infra
```

---

## 👤 User Account Hardening

### 2.1. Remove unnecessary users

```bash
# List users with shells
grep -vE "nologin|false" /etc/passwd | cut -d: -f1

# Delete unused ones (e.g., 'games', 'news')
sudo userdel games
```

### 2.2. Enforce strong password policies

Edit `/etc/pam.d/common-password` and `/etc/pam.d/common-auth` using `libpam-pwquality`:

```bash
sudo apt install libpam-pwquality -y
```

In `/etc/pam.d/common-password`, add/change:

```
password requisite pam_pwquality.so retry=3 minlen=12 difok=3 reject_username enforce_for_root
password sufficient pam_unix.so sha512 shadow use_authtok
```

Enforce password aging in `/etc/login.defs`:

```
PASS_MAX_DAYS   90
PASS_MIN_DAYS   7
PASS_WARN_AGE   14
```

### 2.3. Lock inactive users

```bash
sudo useradd -D -f 30   # disable inactive accounts after 30 days
```

### 2.4. Disable root login

```bash
sudo passwd -l root
sudo usermod --shell /usr/sbin/nologin root
```

Use `sudo` for admin tasks.

### 2.5. Configure `sudo` with restrictions

Edit `/etc/sudoers` with `visudo`:

```bash
Defaults        env_reset,timestamp_timeout=5,tty_tickets
Defaults        log_input,log_output
Defaults        requiretty
```

Limit sudo to specific commands for users:

```
username ALL=(ALL) /usr/bin/systemctl,/usr/bin/apt
```

---

## 🛡️ AppArmor & Kernel Security

### 3.1. Enable and enforce AppArmor

```bash
sudo systemctl enable apparmor --now
sudo aa-status   # Check profiles
```

Enforce all profiles:

```bash
sudo aa-enforce /etc/apparmor.d/*
```

### 3.2. Restrict unprivileged user namespaces (protection against container escape exploits)

```bash
echo "kernel.apparmor_restrict_unprivileged_userns=1" | sudo tee -a /etc/sysctl.d/99-apparmor.conf
sudo sysctl -p /etc/sysctl.d/99-apparmor.conf
```

### 3.3. Disable kernel module loading for non‑root

```bash
echo "kernel.modules_disabled=1" | sudo tee -a /etc/sysctl.d/99-hardening.conf
```

(Requires reboot, careful: prevents all module loading, even legitimate.)

A safer alternative: blacklist dangerous modules:

```bash
echo "blacklist usb-storage" | sudo tee -a /etc/modprobe.d/blacklist.conf
echo "blacklist thunderbolt" | sudo tee -a /etc/modprobe.d/blacklist.conf
```

---

## 🔥 Firewall Configuration (UFW/nftables)

### 4.1. Enable UFW with default deny

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ssh   # Change port if not 22
sudo ufw enable
sudo ufw status verbose
```

### 4.2. Advanced nftables firewall (inline with CIS)

`/etc/nftables.conf`:

```nft
#!/usr/sbin/nft -f

flush ruleset

table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;
        iif lo accept
        ct state established,related accept
        ip protocol icmp icmp type echo-request limit rate 5/second accept
        tcp dport ssh accept
        # Add other services (http, https) if needed
    }
    chain forward {
        type filter hook forward priority 0; policy drop;
    }
    chain output {
        type filter hook output priority 0; policy accept;
    }
}
```

Apply: `sudo nft -f /etc/nftables.conf` and enable systemd unit: `sudo systemctl enable nftables`.

---

## 🔐 SSH Security Hardening

Edit `/etc/ssh/sshd_config`:

```ini
Port 2222                         # Change from default 22
Protocol 2
PermitRootLogin no
MaxAuthTries 3
MaxSessions 5
ClientAliveInterval 300
ClientAliveCountMax 0
LoginGraceTime 30
PubkeyAuthentication yes
PasswordAuthentication no
PermitEmptyPasswords no
ChallengeResponseAuthentication no
UsePAM yes
X11Forwarding no
AllowTcpForwarding no
AddressFamily inet                # IPv4 only
AllowUsers alice bob              # Whitelist specific users
```

Restart: `sudo systemctl restart sshd`

---

## ⚙️ Kernel Hardening (sysctl)

Create `/etc/sysctl.d/99-hardening.conf`:

```ini
# IP spoofing protection
net.ipv4.conf.all.rp_filter=1
net.ipv4.conf.default.rp_filter=1
net.ipv4.conf.all.accept_redirects=0
net.ipv4.conf.default.accept_redirects=0
net.ipv6.conf.all.accept_redirects=0
net.ipv6.conf.default.accept_redirects=0

# Disable source packet routing
net.ipv4.conf.all.accept_source_route=0
net.ipv6.conf.all.accept_source_route=0

# Disable ICMP redirect acceptance
net.ipv4.conf.all.secure_redirects=0
net.ipv4.conf.default.secure_redirects=0

# Log suspicious packets (martians, spoofed)
net.ipv4.conf.all.log_martians=1

# Ignore ICMP ping requests (not essential)
net.ipv4.icmp_echo_ignore_all=0   # change to 1 to block all pings

# Kernel pointer and dmesg restrictions
kernel.kptr_restrict=2
kernel.dmesg_restrict=1

# ASLR (already default, but ensure)
kernel.randomize_va_space=2

# Disable SysRq (can be dangerous)
kernel.sysrq=0

# Restrict ptrace (prevent process injection)
kernel.yama.ptrace_scope=1

# Disable core dumps for setuid programs
fs.suid_dumpable=0

# TCP Hardening
net.ipv4.tcp_syncookies=1
net.ipv4.tcp_sack=0
net.ipv4.tcp_dsack=0
net.ipv4.tcp_timestamps=0
net.ipv4.tcp_rfc1337=1
```

Apply: `sudo sysctl -p /etc/sysctl.d/99-hardening.conf`

---

## 📁 Filesystem & Partition Security

### 6.1. Separate partitions

During installation (or by resizing), create:

- `/` (root) – 30 GB minimum
- `/home` – separate, with `nosuid`, `nodev`, `noexec` options except for specific needs
- `/tmp` – 2 GB, mounted `nosuid,nodev,noexec`
- `/var` – separate, with `nosuid`
- `/var/tmp` – link to `/tmp`

Edit `/etc/fstab`:

```
/dev/sda2 /tmp ext4 defaults,nosuid,nodev,noexec 0 2
```

### 6.2. Mount /tmp as tmpfs (already default)

```bash
echo "tmpfs /tmp tmpfs defaults,noexec,nosuid,nodev 0 0" | sudo tee -a /etc/fstab
```

### 6.3. Disable unused filesystems

```bash
echo "install cramfs /bin/true" | sudo tee -a /etc/modprobe.d/disable-fs.conf
echo "install freevxfs /bin/true" | sudo tee -a /etc/modprobe.d/disable-fs.conf
echo "install jffs2 /bin/true" | sudo tee -a /etc/modprobe.d/disable-fs.conf
echo "install hfs /bin/true" | sudo tee -a /etc/modprobe.d/disable-fs.conf
echo "install hfsplus /bin/true" | sudo tee -a /etc/modprobe.d/disable-fs.conf
```

---

## 🦠 Malware & Rootkit Protection

### 7.1. Install `chkrootkit` and `rkhunter`

```bash
sudo apt install chkrootkit rkhunter lynis -y
sudo rkhunter --propupd
sudo rkhunter --check --skip-keypress
sudo lynis audit system
```

### 7.2. ClamAV (antivirus) for file servers

```bash
sudo apt install clamav clamav-daemon -y
sudo freshclam
sudo systemctl enable clamav-daemon --now
```

Scheduled scan: `crontab -e`:

```
0 2 * * * /usr/bin/clamscan -r /home --quiet
```

---

## 🎯 Threat Modeling & Adversary Profiling

### 8.1. Define assets

- **Personal data**: documents, contacts, browser history, encryption keys.
- **System integrity**: configuration files, kernel, bootloader.
- **Network access**: VPN credentials, SSH keys, firewall.

### 8.2. Adversary types for this setup

| Adversary | Likelihood | Impact | Countermeasures |
|-----------|------------|--------|------------------|
| Script kiddie | High (if exposed) | Low | UFW, strong passwords, updates. |
| Phishing attacks | Medium | Medium | Email filtering, user training, MFA. |
| Targeted attacker | Low | High | Full disk encryption, AppArmor, audit logs. |
| Physical theft | Medium (laptop) | High | FDE, BIOS password, LUKS. |

### 8.3. Risk assessment matrix

Example: *SSH exposed on default port*

- Likelihood: 4 (almost certain for probes)
- Impact: 3 (unauthorised access with weak password)
- Risk score: 12 → **Mitigate** (change port, use keys, fail2ban)

---

## 🔍 OSINT & Counter‑Surveillance Considerations

If your Ubuntu MATE system is used for sensitive work (journalism, activism, research):

- **OSINT on your own system**: Use `netstat -tulpn`, `ss -tulpn`, `lsof -i` to monitor open ports and unexpected listeners.
- **Network surveillance**: Test for DNS leaks (`ipleak.net` over Tor). Use `tcpdump -i eth0` to capture and analyze traffic.
- **Metadata leakage**: Strip EXIF from files before sharing (`exiftool -all= file`). Use `mat2` for anonymising documents.
- **Browser fingerprinting**: Always use Tor Browser for high‑risk browsing. In Firefox, set `privacy.resistFingerprinting = true`.
- **VPN kill switch**: Configure firewall to block all non‑VPN traffic (see nftables example with `oif`).
- **Log cleaning**: Regularly review `/var/log/auth.log`, `syslog`, `kern.log` for anomalies. Consider remote logging (rsyslog to external server).

---

## ✅ Operational Checklists

### ✔️ Initial system setup

- [ ] `sudo apt update && sudo apt full-upgrade -y`
- [ ] `sudo apt install unattended-upgrades` and enable.
- [ ] Set strong password for primary user (12+ chars).
- [ ] Disable root login (`sudo passwd -l root`).
- [ ] Configure `/etc/sudoers` with `visudo`.
- [ ] Enable AppArmor and enforce all profiles.
- [ ] Set kernel sysctl hardening values.
- [ ] Enable UFW (or nftables) and allow only necessary ports.
- [ ] Harden SSH (change port, disable password auth, use keys).
- [ ] Disable unused filesystem modules.
- [ ] Separate `/tmp` partition with `noexec`.
- [ ] Install `rkhunter`, `chkrootkit`, `lynis` and run initial scans.
- [ ] Set up automatic security updates.
- [ ] Verify disk encryption (LUKS) if on laptop.

### ✔️ Weekly maintenance

- [ ] `sudo apt update && sudo apt upgrade` (review if unattended missed).
- [ ] `sudo rkhunter --check`.
- [ ] `sudo lynis audit system` (review recommendations).
- [ ] Check `/var/log/auth.log` for failed login attempts.
- [ ] Verify firewall rules: `sudo ufw status verbose`.
- [ ] Check for open ports: `sudo ss -tulpn`.
- [ ] Review user accounts: `lastlog`, `sudo -l`.

### ✔️ After a security incident

- [ ] Disconnect from network.
- [ ] Preserve logs: `sudo journalctl -b -0 > incident-logs.txt`.
- [ ] Change all passwords (from a clean machine).
- [ ] Reinstall OS if root compromise suspected.
- [ ] Restore from known‑clean backup.
- [ ] Report according to local laws/regulations.

---

## 📚 Further Reading

- **CIS Ubuntu Linux 24.04 LTS Benchmark** (PDF) – [https://www.cisecurity.org/benchmark/ubuntu_linux/](https://www.cisecurity.org/benchmark/ubuntu_linux/)
- **Ubuntu Security documentation** – [https://ubuntu.com/security](https://ubuntu.com/security)
- **Ubuntu Security Guide (USG)** – `sudo apt install usg`
- **EFF Surveillance Self‑Defense** – [https://ssd.eff.org](https://ssd.eff.org)
- **Linux Kernel Self‑Protection Project** – [https://kernsec.org/wiki/index.php/Kernel_Self_Protection_Project](https://kernsec.org/wiki/index.php/Kernel_Self_Protection_Project)

---

*This document is part of the OpSec Documentation Hub. For corrections or additions, please open an issue on GitHub.*
```
