# 💻 Device Hardening

[![Documentation](https://img.shields.io/badge/docs-opsec-blue.svg)](./)
[![License](https://img.shields.io/badge/license-CC%20BY--NC--SA%204.0-lightgrey.svg)](LICENSE)
[![Maintenance](https://img.shields.io/badge/maintained%3F-yes-green.svg)](https://github.com/effjy/opsec/graphs/commit-activity)
[![Hardening](https://img.shields.io/badge/Secure-Configuration-brightgreen.svg)](https://en.wikipedia.org/wiki/Hardening_(computing))

---

## 📋 Table of Contents

1. [🖥️ Core Principles](#core-principles)
2. [🐧 Linux Hardening](#linux-hardening)
3. [🍎 macOS Hardening](#macos-hardening)
4. [🪟 Windows Hardening](#windows-hardening)
5. [📱 Mobile Device Hardening](#mobile-device-hardening)
6. [🔌 BIOS / UEFI Security](#bios--uefi-security)
7. [💾 Disk Encryption](#disk-encryption)
8. [🧹 Data Sanitisation & Secure Deletion](#data-sanitisation--secure-deletion)
9. [✅ Operational Checklists](#operational-checklists)
10. [📚 Further Reading](#further-reading)

---

## 🖥️ Core Principles

Device hardening means reducing the attack surface by disabling unnecessary features, enforcing least privilege, and configuring security controls.

- 🔒 **Least privilege** – Run with minimal necessary privileges.
- 🚫 **Disable what you don’t need** – Every service, port, or feature is a potential entry point.
- 🔄 **Keep updated** – Patches fix known vulnerabilities.
- 📊 **Audit & monitor** – Logs tell you when something goes wrong.
- 💾 **Encrypt at rest** – Protect data if the device is lost or stolen.

> 💡 “A system is only as secure as its weakest component – often an unused service or a forgotten account.”

---

## 🐧 Linux Hardening

### 🔑 Basic steps

| Action | Command / Tool |
|--------|----------------|
| **Keep system updated** | `sudo apt update && sudo apt upgrade` (Debian/Ubuntu) or `sudo dnf update` (Fedora) |
| **Remove unnecessary packages** | `sudo apt autoremove --purge` |
| **Disable root login** | `sudo passwd -l root` |
| **Enforce strong password policy** | Install `libpam-pwquality`, configure `/etc/pam.d/common-password` |
| **Set up `sudo` with restrictions** | `sudo visudo` – limit commands per user or group |
| **Enable firewall** | `sudo ufw enable` (or `nftables` / `iptables`) |
| **Fail2ban for SSH** | `sudo apt install fail2ban` – blocks brute‑force attempts |
| **Disable IPv6 if not needed** | `net.ipv6.conf.all.disable_ipv6 = 1` in `/etc/sysctl.conf` |
| **Secure shared memory** | Add `tmpfs /dev/shm tmpfs defaults,noexec,nosuid,nodev 0 0` in `/etc/fstab` |

### 🔐 SSH hardening

Edit `/etc/ssh/sshd_config`:

```ini
Port 2222                     # Change from default 22
PermitRootLogin no
PasswordAuthentication no     # Use keys only
PubkeyAuthentication yes
MaxAuthTries 3
ClientAliveInterval 300
ClientAliveCountMax 0
AllowUsers alice bob          # Whitelist specific users
```

Restart SSH: `sudo systemctl restart sshd`

### 🔧 Kernel hardening (sysctl)

Add to `/etc/sysctl.conf` or `/etc/sysctl.d/99-security.conf`:

```ini
# IP spoofing protection
net.ipv4.conf.all.rp_filter=1
net.ipv4.conf.default.rp_filter=1

# Ignore ICMP redirects
net.ipv4.conf.all.accept_redirects=0
net.ipv6.conf.all.accept_redirects=0

# Disable source packet routing
net.ipv4.conf.all.accept_source_route=0
net.ipv6.conf.all.accept_source_route=0

# Restrict kernel pointer access (kptr_restrict)
kernel.kptr_restrict=2

# Restrict dmesg access
kernel.dmesg_restrict=1

# Randomise virtual memory layout (ASLR)
kernel.randomize_va_space=2
```

Apply with `sudo sysctl -p`.

### 🛡️ Mandatory Access Control (MAC)

- **AppArmor** (Ubuntu, Debian) – `sudo apt install apparmor-utils`, then enforce profiles.
- **SELinux** (Fedora, RHEL) – `setenforce 1`, audit using `audit2allow`.

### 📦 Auditing tools

- `lynis` – Security auditing and hardening tool.
- `chkrootkit` / `rkhunter` – Rootkit detection.
- `clamav` – Antivirus (optional, but useful for mail servers).

---

## 🍎 macOS Hardening

### 🔑 Basic steps

- **Enable FileVault** (full disk encryption) – System Settings → Privacy & Security → FileVault.
- **Enable firewall** – System Settings → Network → Firewall → On, enable stealth mode.
- **Disable unnecessary sharing** – Sharing pane: turn off Remote Login, Remote Management, Bluetooth Sharing, etc.
- **Install updates automatically** – Software Update → Advanced → Check for updates.
- **Use a standard account** – Not admin for daily use. Create admin separate.

### 🔐 Security settings via `defaults`

```bash
# Disable remote Apple events
sudo systemsetup -setremoteappleevents off

# Disable remote login (SSH)
sudo systemsetup -setremotelogin off

# Disable guest account
sudo defaults write /Library/Preferences/com.apple.AppleFileServer guestAccess -bool NO
sudo defaults write /Library/Preferences/SystemConfiguration/com.apple.ServerServices guestAccess -bool NO

# Show hidden files (but be cautious)
defaults write com.apple.finder AppleShowAllFiles -bool false

# Disable diagnostic submissions
sudo defaults write /Library/Application\ Support/CrashReporter/DiagnosticMessagesHistory.plist AutoSubmit -bool false
```

### 🧰 Tools

- **Little Snitch** – Application firewall (outbound).
- **KnockKnock** – See what's persistently installed.
- **TaskExplorer** – Inspect processes.
- **RansomWhere?** – Detect ransomware behaviour.

### 🔒 Gatekeeper & SIP

- Keep **Gatekeeper** set to “App Store and identified developers”.
- **System Integrity Protection (SIP)** should remain enabled (`csrutil status`). Disable only temporarily for specific low‑level work.

---

## 🪟 Windows Hardening

### 🔑 Basic steps

- **Enable BitLocker** (full disk encryption) – Control Panel → BitLocker Drive Encryption.
- **Configure Windows Defender** – Real‑time protection, cloud delivery, and periodic scanning.
- **Use a standard user account** – Not administrator for daily work.
- **Enable Windows Firewall** – Advanced settings: block inbound, allow outbound only for needed apps.
- **Turn off unnecessary services** – e.g., Print Spooler if not used, Remote Registry, Windows Remote Management.
- **Disable SMBv1** (deprecated and dangerous) – `Set-SmbServerConfiguration -EnableSMB1Protocol $false`.

### 🔐 Group Policy (Windows Pro/Enterprise)

Use `gpedit.msc` to enforce:

| Policy | Setting |
|--------|---------|
| **Account Policies → Password Policy** | Minimum length 12, complexity enabled, maximum age 90 days. |
| **Security Options → Interactive logon** | Do not display last user name. |
| **Network Security → LAN Manager authentication** | Send NTLMv2 only, refuse LM and NTLM. |
| **Microsoft Defender → Real‑time protection** | Enabled. |
| **Windows Update → Configure Automatic Updates** | Auto download and install. |

### 🧰 Tools

- **Sysinternals Suite** – Autoruns, Process Explorer, TCPView.
- **HardenTools** (by security experts) – Script to apply recommended settings.
- **O&O ShutUp10++** – Disable telemetry and privacy‑invasive features (Windows 10/11).
- **WPD (Windows Privacy Dashboard)** – Another telemetry blocker.

### 📡 Disable telemetry (Windows 10/11)

- Set to “Security” level (basic error reporting) via Group Policy:  
  `Computer Configuration → Administrative Templates → Windows Components → Data Collection and Preview Builds → Allow Telemetry → Security`.
- Use **WPD** or **O&O ShutUp10++** for GUI.

---

## 📱 Mobile Device Hardening

### 📱 iOS (iPhone/iPad)

- **Keep iOS updated**.
- **Use strong alphanumeric passcode** (Settings → Face ID & Passcode → Change Passcode → Passcode Options → Custom Alphanumeric).
- **Enable “Erase Data”** – Wipe after 10 failed attempts.
- **Disable USB accessories when locked** – Settings → Face ID & Passcode → USB Accessories → OFF.
- **Disable Siri when locked**.
- **Limit lock screen access** – Control Centre, Today View, Notification Centre.
- **Turn off analytics & share with Apple** – Privacy → Analytics & Improvements → disable all.
- **Use Lockdown Mode** (iOS 16+) for high‑risk profiles – Settings → Privacy & Security → Lockdown Mode.

### 🤖 Android

- **Keep system & Play Store updates**.
- **Use strong PIN/password** (not pattern – patterns are easily shoulder‑surfed).
- **Enable “Find My Device”**.
- **Disable USB debugging** (except during development).
- **Install apps only from Play Store** – disable “Unknown sources”.
- **Use a firewall (e.g., NetGuard, NoRoot Firewall)** to block apps’ internet access.
- **Disable Google Location History & Advertising ID reset** – Settings → Location → Google Location History.
- **Use Android Enterprise with work profile** to isolate work/personal.

### 🌐 Both platforms

- **Use a reputable VPN** (or Tor via Orbot on Android).
- **Disable Bluetooth when not in use**.
- **Use encrypted messengers** (Signal, Wire, Threema).
- **Review app permissions regularly** – revoke unnecessary contacts, camera, microphone, location access.
- **Consider GrapheneOS (Android)** or **CalyxOS** for privacy‑hardened OS.

---

## 🔌 BIOS / UEFI Security

The firmware is the lowest layer – if it’s compromised, nothing above is trustworthy.

### 🔑 Hardening steps

1. **Set a supervisor password** – Prevents unauthorised changes to boot order or settings.
2. **Disable boot from USB / DVD / network** – Only boot from internal drive.
3. **Enable Secure Boot** – Prevents unsigned bootkits.
4. **Enable TPM (Trusted Platform Module)** – Used by BitLocker, system integrity measurements.
5. **Disable Intel ME / AMD PSP** if possible (advanced, may affect functionality).
6. **Update firmware** – Check manufacturer periodically for security updates.

### 🧠 Additional measures

- **Set a BIOS/UEFI password** – Without it, an attacker with physical access can reset the machine.
- **Enable “Boot guard”** (if available) – Intel Boot Guard or AMD Hardware Root of Trust.
- **Consider Coreboot / Libreboot** for open‑source firmware (requires compatible hardware).

---

## 💾 Disk Encryption

Encrypting the entire drive protects data at rest – when the device is off, suspended, or the drive is removed.

### 🔒 Full Disk Encryption (FDE)

| OS | Built‑in solution | Notes |
|----|-------------------|-------|
| Windows | **BitLocker** | Requires TPM; Pro/Enterprise editions. |
| macOS | **FileVault 2** | XTS‑AES‑128; enabled during setup. |
| Linux (LUKS) | **LUKS (cryptsetup)** | Use `cryptsetup luksFormat` – AES‑256‑XTS recommended. |
| Android | **File‑based encryption** | Enabled by default on modern devices (Android 10+). |
| iOS | **Data Protection** | Hardware‑encrypted; passcode derived key. |

### 📦 File‑level encryption (for specific files/folders)

- **VeraCrypt** – Cross‑platform, creates encrypted containers or encrypts partitions (supports hidden volumes).
- **Cryptomator** – Client‑side encryption for cloud sync.
- **gocryptfs** – Linux; encrypts folder as a FUSE mount.

> ⚠️ **Always encrypt before storing sensitive data on removable media (USB drives).** Use LUKS, VeraCrypt, or BitLocker To Go.

### 🔄 When to encrypt

- **At rest** – On laptops (high risk of theft), removable drives, backups.
- **In transit** – Not disk encryption – that’s for network (TLS, VPN).
- **In memory** – Not covered; assumes device is powered off or suspended.

---

## 🧹 Data Sanitisation & Secure Deletion

Deleting a file normally just removes the pointer – data remains until overwritten.

### 🗑️ Secure deletion tools

| OS | Tool | Command |
|----|------|---------|
| Linux | `shred` | `shred -vfz -n 7 file` (overwrite 7 passes, zero at end) |
| Linux | `wipe` | `wipe -rf dir/` |
| Linux | `srm` (secure‑remove) | `srm -vz file` |
| macOS | `srm` (deprecated) | Use `rm -P` (overwrite single pass) or `diskutil secureErase` |
| Windows | `cipher /w:C` | Overwrites free space on C: drive |
| Windows | `SDelete` (Sysinternals) | `sdelete -p 3 file` |
| Cross‑platform | **BleachBit** | GUI + CLI; wipes free space and files. |

### 🔥 Whole‑drive sanitisation (e.g., before disposal)

- **ATA Secure Erase** (for SSDs) – `hdparm --user-master u --security-set-pass p /dev/sdX` then `--security-erase`.
- **NVMe Format** – `nvme format /dev/nvme0n1` (with persistent erase).
- **DBAN** (Darik’s Boot and Nuke) – for HDDs; not recommended for SSDs.

> 🚨 **SSDs wear leveling** means overwriting a file may not erase all copies. Use ATA Secure Erase or full disk encryption from the start (then just throw away the key).

---

## ✅ Operational Checklists

### ✔️ Initial device setup (any OS)

- [ ] Change default passwords.
- [ ] Install all security updates.
- [ ] Enable full disk encryption.
- [ ] Create a standard (non‑admin) user account.
- [ ] Disable unnecessary services (e.g., Bluetooth, printing, remote access).
- [ ] Configure firewall (inbound block, outbound default allow but monitor).
- [ ] Set up automatic updates.
- [ ] Install a reputable antivirus/anti‑malware (if not built‑in).
- [ ] Configure screensaver lock with password (5 minutes).

### ✔️ Weekly / monthly maintenance

- [ ] Check for OS and application updates.
- [ ] Review installed applications – remove unused.
- [ ] Check logs for suspicious entries (`/var/log/auth.log`, Event Viewer).
- [ ] Verify backup integrity (test restore).
- [ ] Review device permissions (camera, mic, location, contacts).

### ✔️ Travel / temporary high‑risk scenario

- [ ] Backup data and remove sensitive files.
- [ ] Use a separate travel device or encrypted VM.
- [ ] Enable full disk encryption + strong password.
- [ ] Disable biometrics (fingerprint, face unlock).
- [ ] Use a hardware security key (YubiKey) for logins.
- [ ] Consider using Tails or a live USB for the trip.

### ✔️ Before disposing of a device

- [ ] Deauthorise the device from iCloud, Google, Microsoft accounts.
- [ ] Perform a secure erase (ATA Secure Erase for SSD, DBAN for HDD).
- [ ] For laptops, remove or physically destroy the drive if the data is highly sensitive.
- [ ] Factory reset only after secure erase (for smart devices).

---

## 📚 Further Reading

- **🐧 Linux Hardening Guide (CIS Benchmarks)** – [https://www.cisecurity.org/benchmark/distribution_independent_linux/](https://www.cisecurity.org/benchmark/distribution_independent_linux/)
- **🍎 Apple Platform Security** – [https://support.apple.com/guide/security/welcome/web](https://support.apple.com/guide/security/welcome/web)
- **🪟 Windows Security Baselines** – [https://learn.microsoft.com/en-us/windows/security/operating-system-security/device-management/windows-security-baselines](https://learn.microsoft.com/en-us/windows/security/operating-system-security/device-management/windows-security-baselines)
- **🔒 NIST SP 800‑88 Rev. 1 (Media Sanitisation)** – [https://csrc.nist.gov/publications/detail/sp/800-88/rev-1/final](https://csrc.nist.gov/publications/detail/sp/800-88/rev-1/final)
- **🧽 BleachBit** – [https://www.bleachbit.org/](https://www.bleachbit.org/)
- **🔐 VeraCrypt Documentation** – [https://www.veracrypt.fr/en/Documentation.html](https://www.veracrypt.fr/en/Documentation.html)

---

*This document is part of the OpSec Documentation Hub. For corrections or additions, please open an issue on GitHub.*
```
