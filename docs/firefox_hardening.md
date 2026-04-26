# 🦊 Mozilla Firefox OpSec Hardening Guide

[![Documentation](https://img.shields.io/badge/docs-opsec-blue.svg)](./)
[![License](https://img.shields.io/badge/license-CC%20BY--NC--SA%204.0-lightgrey.svg)](../LICENSE)
[![Firefox](https://img.shields.io/badge/Firefox-Latest-FF7139?logo=firefox&logoColor=white)](https://www.mozilla.org/firefox/)
[![Privacy](https://img.shields.io/badge/Privacy-Hardened-brightgreen.svg)](https://www.mozilla.org/en-US/firefox/privacy/)

---

## 📋 Table of Contents

1. [🔑 Core Principles](#core-principles)
2. [⚙️ Initial Setup & Privacy Preferences](#initial-setup--privacy-preferences)
3. [🔧 Essential `about:config` Tweaks](#essential-aboutconfig-tweaks)
4. [🧩 Recommended Extensions](#recommended-extensions)
5. [🌐 DNS over HTTPS (DoH) Configuration](#dns-over-https-doh-configuration)
6. [📦 Containers & Multi‑Account Isolation](#containers--multi-account-isolation)
7. [🛡️ Anti‑Fingerprinting & Anti‑Tracking](#anti-fingerprinting--anti-tracking)
8. [🗑️ Data Sanitisation & Session Management](#data-sanitisation--session-management)
9. [📱 Sync & Account Security](#sync--account-security)
10. [✅ Operational Checklists](#operational-checklists)
11. [📚 Further Reading](#further-reading)

---

## 🔑 Core Principles

Firefox is one of the most privacy‑respecting mainstream browsers, but defaults are not enough for OpSec. Hardening involves:

- **Disabling telemetry and data collection**.
- **Reducing fingerprinting surface**.
- **Isolating identities** (containers, profiles).
- **Forcing encrypted connections**.
- **Controlling third‑party content**.

> ⚠️ **Warning**: Some tweaks may break websites. Use **separate profiles** for daily browsing vs. sensitive work.

---

## ⚙️ Initial Setup & Privacy Preferences

### 1.1. Settings → Privacy & Security

| Setting | Recommended value |
|---------|-------------------|
| **Enhanced Tracking Protection** | Strict |
| **Do Not Track** | Send a Do Not Track signal (advisory, but harmless). |
| **Cookies and site data** | Block third‑party cookies (or use “Delete cookies and site data when Firefox is closed”). |
| **Cross‑site tracking cookies** | Block |
| **Cryptominers** | Block |
| **Fingerprinters** | Block |
| **History** | Use “Never remember history” (Private Browsing mode equivalent) or “Use custom settings” with “Clear history when Firefox closes”. |
| **Address bar** | Uncheck “Suggest history”, “Suggest bookmarks”, “Suggest open tabs”, “Suggest search engines”. |
| **Firefox Data Collection and Use** | **Disable all** (including “Allow Firefox to send technical data”). |

### 1.2. Permissions

In *Privacy & Security* → *Permissions*, manually review and disable unused permissions:

- **Location**, **Camera**, **Microphone**, **Notifications**, **Autoplay** – set “Block new requests” or ask for permission each time.

### 1.3. HTTPS‑Only Mode

Enable **HTTPS‑Only Mode** in all windows.  
*Settings → Privacy & Security → HTTPS‑Only Mode → Enable HTTPS‑Only Mode in all windows.*

---

## 🔧 Essential `about:config` Tweaks

Type `about:config` in the address bar, accept the risk, then search and modify:

### 2.1. Telemetry & Data Collection

```
toolkit.telemetry.enabled = false
datareporting.healthreport.uploadEnabled = false
datareporting.policy.dataSubmissionEnabled = false
browser.newtabpage.activity-stream.feeds.telemetry = false
browser.newtabpage.activity-stream.telemetry = false
browser.ping-centre.telemetry = false
browser.search.widget.inNavBar = false
```

### 2.2. Fingerprinting Resistance

```
privacy.resistFingerprinting = true           # Strong (may break sites)
webgl.disabled = true                         # Disable WebGL (major fingerprint vector)
media.peerconnection.enabled = false          # Disable WebRTC (leaks real IP behind VPN)
geo.enabled = false
browser.cache.offline.enable = false
dom.battery.enabled = false
device.sensors.enabled = false
dom.vr.enabled = false
```

### 2.3. Network & Security

```
network.http.referer.XOriginPolicy = 2        # Send referer only on same origin
network.http.referer.trimmingPolicy = 2       # Trim referer to origin
network.cookie.cookieBehavior = 1             # Reject third‑party cookies
network.cookie.lifetimePolicy = 2             # Delete cookies on exit
dom.security.https_only_mode = true
security.ssl.enable_ocsp_stapling = true
security.tls.enable_0rtt_data = false         # Disable 0‑RTT (privacy risk)
network.trr.mode = 3                          # Use DoH only (no fallback) – see §5
```

### 2.4. Disable IPv6 Leaks (if not needed)

```
network.dns.disableIPv6 = true
```

### 2.5. Disable Link Prefetching & Preloading

```
network.preload = false
network.prefetch-next = false
network.dns.disablePrefetch = true
network.predictor.enabled = false
```

### 2.6. Other Privacy Booleans

```
browser.send_pings = false                    # Disable hyperlink auditing
dom.event.clipboardevents.enabled = false     # Prevent sites from reading clipboard
full-screen-api.enabled = false               # Disable full‑screen API (phishing risk)
middlemouse.contentLoadURL = false            # Prevent accidental loads
```

---

## 🧩 Recommended Extensions

| Extension | Purpose |
|-----------|---------|
| **uBlock Origin** (strict mode) | Blocks ads, trackers, malicious domains. Enable all filter lists. |
| **NoScript** | Whitelist only trusted domains; blocks JavaScript, WebGL, WebRTC by default. |
| **CanvasBlocker** | Spoofs canvas, WebGL, audio fingerprints. |
| **ClearURLs** | Removes tracking parameters from URLs. |
| **Cookie AutoDelete** | Automatically deletes cookies when tab closes. |
| **Multi‑Account Containers** (official) | Isolate sites into separate containers (see §6). |
| **Decentraleyes** (or **LocalCDN**) | Serves local copies of CDN libraries to prevent tracking. |
| **Neat URL** | Alternative to ClearURLs. |
| **Don't Track Me Google** | Removes Google's tracking links. |

> 💡 **Minimalism**: Fewer extensions mean lower attack surface. Start with uBlock Origin + NoScript + Container tabs.

---

## 🌐 DNS over HTTPS (DoH) Configuration

DoH prevents your ISP from seeing your DNS queries. Firefox has built‑in support.

### 4.1. Enable via settings

*Settings → Privacy & Security → DNS over HTTPS* → Enable, choose provider:

- **Cloudflare** – fastest, logs minimal data (but retains for 24h).
- **NextDNS** – customizable, supports logging control.
- **Quad9** – blocks malicious domains.
- **Custom** (e.g., `https://dns.quad9.net/dns-query`)

### 4.2. Enforce via `about:config`

```
network.trr.mode = 3          # Use DoH only, fallback blocked
network.trr.uri = "https://dns.quad9.net/dns-query"
```

> ⚠️ Mode 3 may break captive portals. Use mode 2 (fallback to system DNS) for more compatibility.

---

## 📦 Containers & Multi‑Account Isolation

**Firefox Multi‑Account Containers** isolate different identities into separate “containers”. Cookies, storage, and cache are not shared between containers.

### 5.1. Basic usage

- Install the official extension.
- Create containers: Work, Personal, Banking, Research, Social.
- Always open websites in the appropriate container.
- Use **Containerise** or **Container Bookmarks** to force specific sites into fixed containers.

### 5.2. Temporary containers

Add **Temporary Containers** extension – every new tab gets a fresh, disposable container. Closes automatically, destroying all traces.

### 5.3. For high OpSec

- **Separate Firefox profiles**: Each profile has its own settings, extensions, containers.
  - Create profile: `firefox -P` → Create Profile.
  - Launch dedicated profile: `firefox -P "Research" --no-remote`.
- **Use different Firefox versions** for different compartments (e.g., Stable for normal, Developer Edition for research, Tor Browser for anonymous).

---

## 🛡️ Anti‑Fingerprinting & Anti‑Tracking

Even with `privacy.resistFingerprinting = true`, additional steps help.

### 6.1. Spoof User‑Agent

`about:config` → `general.useragent.override` – set to a common string (e.g., Windows 10 + Chrome) to blend in. However, resistFingerprinting already does this.

### 6.2. Disable JavaScript (via NoScript)

For truly sensitive browsing, set NoScript to “Default to blocked” for all scripts, frames, fonts, WebGL.

### 6.3. Limit timezone and language

```
privacy.spoof_english = 2                     # Spoof English locale
privacy.resistFingerprinting.autoDeclineNoUserInputCanvasPrompts = true
```

### 6.4. Disable WebRTC (already covered)

Test WebRTC leaks at [browserleaks.com/webrtc](https://browserleaks.com/webrtc).

---

## 🗑️ Data Sanitisation & Session Management

### 7.1. Automatically clear data on exit

*Settings → Privacy & Security → History → Use custom settings* → Check “Clear history when Firefox closes”. Click “Settings…” and select:

- ✓ Browsing & download history
- ✓ Cookies (unless you use Cookie AutoDelete)
- ✓ Cache
- ✓ Active Logins
- ✓ Form & Search History
- ✓ Site preferences

### 7.2. Manual wiping (shortcut)

`Ctrl+Shift+Del` → Select time range “Everything” → check all items → Clear.

### 7.3. Disable session restore

`about:config` → `browser.sessionstore.resume_from_crash = false`. Prevents accidental session remnants.

---

## 📱 Sync & Account Security

If you use Firefox Sync:

- Use a **strong, unique password**.
- Enable **two‑step verification** for your Firefox account (TOTP).
- Do **not** sync across compartments (use separate Firefox accounts for different profiles).
- Encrypt sync data with a **recovery key** (stored offline).

### Disable Sync if not needed

*Settings → Sync* → Disconnect.

---

## ✅ Operational Checklists

### ✔️ New Firefox profile creation (for sensitive work)

- [ ] `firefox -P` → Create profile (e.g., “Secure”).
- [ ] Apply all `about:config` tweaks above.
- [ ] Set privacy preferences to “Strict” and disable all telemetry.
- [ ] Install uBlock Origin, NoScript, Multi‑Account Containers, CanvasBlocker.
- [ ] Configure uBlock in “hard mode” (block all third‑party scripts/frames).
- [ ] Set NoScript to “Default to blocked”.
- [ ] Enable HTTPS‑Only mode.
- [ ] Disable Firefox Sync.
- [ ] Clear all data on exit.
- [ ] Test fingerprint at [amiunique.org](https://amiunique.org) and [browserleaks.com](https://browserleaks.com).

### ✔️ Daily browsing (medium security)

- [ ] Use **private window** (`Ctrl+Shift+P`) for single‑session tasks.
- [ ] Containers: use separate container for each identity.
- [ ] NoScript: temporarily allow only essential domains.
- [ ] Clear cookies and cache before closing.
- [ ] Disable location and camera permissions.

### ✔️ Weekly maintenance

- [ ] Update Firefox (Settings → About Firefox).
- [ ] Review extension permissions.
- [ ] Clear all browsing data (manual wipe).
- [ ] Run a fingerprint check.
- [ ] Check `about:telemetry` for unexpected outgoing data (should be minimal after tweaks).

---

## 📚 Further Reading

- **Arkenfox user.js** – The most comprehensive Firefox hardening project: [https://github.com/arkenfox/user.js](https://github.com/arkenfox/user.js)
- **Privacy Guides’ Firefox Hardening** – [https://www.privacyguides.org/desktop-browsers/#firefox-hardening](https://www.privacyguides.org/desktop-browsers/#firefox-hardening)
- **Mozilla’s own privacy settings explainer** – [https://support.mozilla.org/en-US/kb/private-browsing-use-firefox-without-history](https://support.mozilla.org/en-US/kb/private-browsing-use-firefox-without-history)
- **BrowserLeaks** – Test fingerprint, WebRTC, and more: [https://browserleaks.com](https://browserleaks.com)

---

*This document is part of the OpSec Documentation Hub. For corrections or additions, please open an issue on GitHub.*
```
