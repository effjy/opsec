# ✅ OpSec Checklists

[![Documentation](https://img.shields.io/badge/docs-opsec-blue.svg)](./)
[![License](https://img.shields.io/badge/license-CC%20BY--NC--SA%204.0-lightgrey.svg)](LICENSE)
[![Maintenance](https://img.shields.io/badge/maintained%3F-yes-green.svg)](https://github.com/effjy/opsec/graphs/commit-activity)
[![Checklists](https://img.shields.io/badge/Checklists-Essential-brightgreen.svg)](https://en.wikipedia.org/wiki/Checklist)

---

## 📋 Table of Contents

1. [🕒 Daily OpSec Checklist](#daily-opsec-checklist)
2. [📅 Weekly OpSec Checklist](#weekly-opsec-checklist)
3. [🌙 Monthly / Quarterly Checklist](#monthly--quarterly-checklist)
4. [✈️ Travel Security Checklist](#travel-security-checklist)
5. [🚨 Incident Response Checklist](#incident-response-checklist)
6. [🆕 New Identity / Persona Creation Checklist](#new-identity--persona-creation-checklist)
7. [💻 Device Setup & Hardening Checklist](#device-setup--hardening-checklist)
8. [🔑 Password & Key Management Checklist](#password--key-management-checklist)
9. [📎 Offboarding & Deletion Checklist](#offboarding--deletion-checklist)
10. [📚 Further Reading](#further-reading)

---

## 🕒 Daily OpSec Checklist

**Every day, before starting work / sensitive activities:**

- [ ] 🔒 VPN connected (check IP, DNS leak test).
- [ ] 🧅 Tor Browser updated (if used).
- [ ] 📱 Phone: Bluetooth off, Wi‑Fi scanning disabled, location off (unless needed).
- [ ] 🖥️ Computer: Firewall active, no unexpected processes (`top`, `taskmgr`).
- [ ] 🔐 2FA tokens ready (YubiKey, TOTP app).
- [ ] 🗑️ Browser history and cookies cleared (or use private/incognito mode).
- [ ] 📁 No sensitive files on desktop – moved to encrypted container.
- [ ] 🔌 USB drives not left plugged in.
- [ ] 📸 Webcam covered (physical tape or shutter).
- [ ] 🎙️ Microphone muted / disabled if not in use.

**End of day:**

- [ ] 💾 Save all work, commit to encrypted storage.
- [ ] 🧹 Clear clipboard (copy a dummy text or use `echo "" | clip`).
- [ ] 🔒 Lock screen (Win+L, Ctrl+Cmd+Q, or `gnome-screensaver-command -l`).
- [ ] 🚪 Log out of all online accounts (email, social, cloud).
- [ ] 🛑 Disconnect VPN (or leave connected if policy requires).
- [ ] 📴 Power off devices or put them in a faraday bag for high‑risk environments.

---

## 📅 Weekly OpSec Checklist

**Once per week:**

- [ ] 🔄 **Update all software** – OS, browsers, VPN, messaging apps, anti‑virus.
- [ ] 🔍 **Check for security bulletins** – CVE feeds, vendor advisories.
- [ ] 🧹 **Clean temporary files** – Clear browser caches, download folders, temp directories.
- [ ] 🔐 **Rotate work‑related passwords** (if policy mandates). At least review them.
- [ ] 📧 **Review email forwarding rules** – Check for unauthorised forwards.
- [ ] 🕵️ **Check active sessions / logged‑in devices** – Google, Microsoft, Apple, social accounts.
- [ ] 🛡️ **Run a security scan** – ClamAV (Linux), Windows Defender (full scan), Malwarebytes (optional).
- [ ] 🗂️ **Backup critical data** – Encrypted backup to external drive or cloud.
- [ ] 🔎 **Review firewall logs** – Look for unexpected outbound connections.
- [ ] 📊 **Check disk space and unusual files** (ransomware indicators).

---

## 🌙 Monthly / Quarterly Checklist

**Every month (or at least quarterly):**

- [ ] 🔑 **Audit user accounts** – Remove inactive accounts, enforce strong passwords.
- [ ] 📜 **Review access logs** – SSH, VPN, critical applications.
- [ ] 🧪 **Test backups** – Restore a random file from backup to ensure integrity.
- [ ] 🛡️ **Run a vulnerability scanner** (OpenVAS, Lynis, Nessus) on your systems.
- [ ] 🔁 **Rotate high‑value encryption keys** (PGP, SSH, disk encryption).
- [ ] 📱 **Check for app permissions** – Revoke unnecessary camera, mic, location access.
- [ ] 🌐 **Review DNS settings** – Are they still pointing to trusted resolvers?
- [ ] 🔒 **Update your threat model** – Any new adversaries, assets, or vulnerabilities?
- [ ] 🧹 **Review and prune firewall rules** – Remove unneeded allowances.
- [ ] 📄 **Check for sensitive data exposure** – Use `truffleHog`, `git-secrets` on repos.
- [ ] 🧑‍💻 **Revoke old SSH keys and API tokens**.
- [ ] 📬 **Verify that email aliases / forwards still point correctly** (no hijacking).

---

## ✈️ Travel Security Checklist

**Before travelling (especially internationally):**

- [ ] 🔐 **Backup data & perform secure deletion** of unnecessary files.
- [ ] 💻 **Use a travel laptop / burn‑er device** – No sensitive data on it.
- [ ] 🧹 **Wipe browser history, saved passwords, cookies**.
- [ ] 🔑 **Use a strong, unique boot password** (different from usual).
- [ ] 🔒 **Enable full disk encryption** (if not already).
- [ ] 📱 **Remove SIM card** (use local pre‑paid SIM instead of home SIM).
- [ ] 🚫 **Disable biometric unlocks** (fingerprint, face) – use strong passphrase.
- [ ] 🔌 **Pack a faraday bag** for phones/laptops when not in use.
- [ ] 🖥️ **Set up a temporary VPN** in a friendly jurisdiction.
- [ ] 📇 **Carry only essential documents** – No unnecessary IDs, credit cards.
- [ ] 💵 **Bring local currency and a backup USD/EUR stash**.
- [ ] 🔋 **Charge all devices and bring power banks** (no charging from unknown USB ports).
- [ ] 🗺️ **Learn local laws** regarding encryption, Tor, VPNs.

**During travel:**

- [ ] 🚪 Never leave devices unattended in hotel rooms (use safe or take with you).
- [ ] 🛑 At border: Power off devices before inspection. Use hidden volumes if paranoid.
- [ ] 🔌 Use only your own chargers, not public USB ports (use AC outlet with your own adapter).
- [ ] 🛜 Use hotel Wi‑Fi only through **VPN** (prefer cellular hotspot if possible).
- [ ] 🚫 Disable Bluetooth and NFC.
- [ ] 📍 Turn off location services unless necessary.
- [ ] 🎭 Use a travel‑specific pseudonym if booking flights/hotels.
- [ ] 📞 Have a pre‑planned emergency contact protocol.

**After returning:**

- [ ] 🔄 Reset all passwords used during travel.
- [ ] 🔍 Scan devices for malware / trackers.
- [ ] 🗑️ Wipe the travel device (if burner) or restore from pre‑travel backup.
- [ ] 📞 Discard travel SIM card.
- [ ] 🕵️ Review credit card and bank statements for unauthorised activity.

---

## 🚨 Incident Response Checklist

**Immediate steps (first 10 minutes):**

- [ ] 🛑 **Disconnect from network** (pull Ethernet, disable Wi‑Fi, turn off Bluetooth).
- [ ] 🔒 **Power down device** if advanced persistent threat suspected – but only after preserving volatile evidence (if you need forensics).
- [ ] 📞 **Notify your security contact / team** (coded message).
- [ ] 🔐 **Change critical passwords** (email, banking, VPN) from a **clean, uncompromised device**.
- [ ] 🗑️ **Revoke all session tokens** (force logouts from all devices via “sign out everywhere”).
- [ ] 📸 **Take photos** of screen, error messages, physical surroundings (if physical breach).
- [ ] 📝 **Write down timeline** – what happened, when, what you did.

**Within 1 hour:**

- [ ] 🧹 **Preserve logs** – copy to external media (read‑only mount).
- [ ] 🕵️ **Isolate affected system** – keep powered off or on a segregated VLAN.
- [ ] 🔬 **Start forensic capture** (memory dump, disk image) if within capability.
- [ ] 📄 **Check for data exfiltration** – large outbound transfers, new files, unusual processes.
- [ ] 🚫 **Disable compromised accounts**.
- [ ] 🛡️ **Implement temporary containment** – firewall rules to block attacker IPs.
- [ ] 📢 **Prepare internal communication** (need‑to‑know basis).

**Within 24 hours:**

- [ ] 🧪 **Analyse root cause** – review logs, check for known vulnerabilities.
- [ ] 🔄 **Restore from clean backup** (last known good state).
- [ ] 🔑 **Reset ALL passwords** (including service accounts, API keys).
- [ ] 🧹 **Re‑image compromised systems** (full wipe and reinstall).
- [ ] 📈 **Notify affected parties** (customers, partners, regulators) – consult legal.
- [ ] 📄 **Update incident report** with timeline, impact, recovery steps.
- [ ] 🧠 **Conduct lessons learned** – update threat model and countermeasures.

---

## 🆕 New Identity / Persona Creation Checklist

- [ ] 🎯 **Define purpose** – Temporary, long‑term, one‑use.
- [ ] 🕵️ **Choose pseudonym** – Doesn’t resemble real name, not used elsewhere.
- [ ] 📧 **Create email** – ProtonMail / Tutanota over Tor, no recovery phone/email.
- [ ] 🔑 **Generate strong password** – 20+ characters, stored in encrypted vault.
- [ ] 📱 **Get a burner phone / VoIP number** (if needed) – pay with cryptocurrency.
- [ ] 🌐 **Register accounts** (social, forums, services) using the pseudonym and burner email.
- [ ] 🎭 **Build legend** – backstory, interests, fake birthday, location.
- [ ] 📸 **Use generic or AI‑generated avatar** – no reverse image search.
- [ ] 🧹 **Seed history** – post neutral content, join groups, follow people.
- [ ] 📦 **Separate device or VM** – dedicated for this persona.
- [ ] 🔐 **Document persona details** in encrypted container (Veracrypt).
- [ ] 🚫 **Never cross‑contaminate** – no logging into real accounts with this browser.

---

## 💻 Device Setup & Hardening Checklist

**For a new computer (or re‑imaged):**

- [ ] 🖥️ **Install OS** – prefer Linux (Qubes, Fedora, Ubuntu) or macOS; Windows with careful hardening.
- [ ] 🔒 **Enable full disk encryption** – LUKS, BitLocker, FileVault.
- [ ] 🔑 **Set strong BIOS/UEFI password** – disable boot from USB/DVD.
- [ ] 🚫 **Disable unnecessary hardware** – webcam, microphone, Bluetooth, Thunderbolt (if not used).
- [ ] 🔥 **Configure firewall** – default deny inbound, restrict outbound.
- [ ] 🛡️ **Install and configure security software** – ClamAV (Linux), Windows Defender, Little Snitch (macOS).
- [ ] 🔄 **Enable automatic updates** – OS and applications.
- [ ] 👤 **Create a standard user account** – use admin only for tasks.
- [ ] 🔐 **Set up password policy** – 12+ chars, lock after failed attempts.
- [ ] 📂 **Create encrypted containers** for sensitive data (Veracrypt, CryFS).
- [ ] 🗑️ **Install secure deletion tool** – `shred`, `srm`, `bleachbit`.
- [ ] 🧅 **Install Tor Browser** (if needed) and set security to “Safest”.
- [ ] 🔌 **Disable IPv6** unless required.
- [ ] 🕵️ **Test DNS leaks** and fingerprint resistance.
- [ ] 📦 **Make a clean backup image** before connecting to internet.

---

## 🔑 Password & Key Management Checklist

- [ ] 🗄️ **Use a password manager** – Bitwarden (self‑hosted), KeePassXC, 1Password.
- [ ] 🔐 **Master password** – 20+ characters, memorised, not written down.
- [ ] 🔑 **Enable 2FA** – Hardware token (YubiKey) or TOTP (Aegis, Raivo) for the password manager itself.
- [ ] 🧹 **Remove old / weak passwords** – Use “password health” feature.
- [ ] 🔁 **Rotate critical passwords** every 90 days (email, banking, VPN).
- [ ] 🆔 **Generate unique passwords** per site – at least 16 characters.
- [ ] 🔢 **Use passphrases** for things you must remember (Diceware 6+ words).
- [ ] 🧠 **Store recovery codes** offline (paper, in safe).
- [ ] 🔑 **SSH keys** – Use Ed25519, protect with passphrase, add to SSH agent only temporarily.
- [ ] 🗝️ **GPG / PGP keys** – Keep on hardware token (YubiKey) or encrypted USB.
- [ ] 📜 **Document key escrow** (if organisation) – secure split.

---

## 📎 Offboarding & Deletion Checklist

**When leaving an organisation or retiring a persona:**

- [ ] 📧 **Forward or back up important emails** (then delete all from server).
- [ ] 📁 **Transfer or destroy all files** on work devices (follow policy).
- [ ] 🧹 **Securely wipe** – `shred`, `srm`, or ATA secure erase.
- [ ] 🔑 **Revoke all access** – SSH keys, API tokens, 2FA seeds.
- [ ] 🔄 **Change shared service passwords** that you knew.
- [ ] 📱 **Sign out of all accounts** on all devices.
- [ ] 🗑️ **Delete personal data** from browsers (saved passwords, history, autofill).
- [ ] 📞 **Unlink personal phone** from work accounts (authenticator apps).
- [ ] 📤 **Remove own access** from cloud storage shares.
- [ ] ✔️ **Get written confirmation** of offboarding (if corporate).
- [ ] 🔥 **Burn the persona** – delete social media, email, forum accounts.

---

## 📚 Further Reading

- **📖 “The Checklist Manifesto” by Atul Gawande** – General checklist philosophy.
- **🛡️ OWASP Cheat Sheets** – [https://cheatsheetseries.owasp.org](https://cheatsheetseries.owasp.org)
- **📋 EFF’s Surveillance Self‑Defense Checklists** – [https://ssd.eff.org](https://ssd.eff.org)
- **✅ README.md of this repository** – Includes links to all topic guides.

---

*This document is part of the OpSec Documentation Hub. Use these checklists as templates – adapt them to your own threat model and environment.*
```
