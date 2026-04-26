# 🌐 Network OpSec

[![Documentation](https://img.shields.io/badge/docs-opsec-blue.svg)](./)
[![License](https://img.shields.io/badge/license-CC%20BY--NC--SA%204.0-lightgrey.svg)](LICENSE)
[![Maintenance](https://img.shields.io/badge/maintained%3F-yes-green.svg)](https://github.com/effjy/opsec/graphs/commit-activity)
[![Privacy](https://img.shields.io/badge/Privacy-First-brightgreen.svg)](https://en.wikipedia.org/wiki/Privacy)

---

## 📋 Table of Contents

1. [🔑 Core Principles](#core-principles)
2. [🔒 VPNs – Capabilities & Limitations](#vpns--capabilities--limitations)
3. [🧅 Tor – The Onion Router](#tor--the-onion-router)
4. [🌍 DNS & Leak Prevention](#dns--leak-prevention)
5. [📡 Wi‑Fi Hardening](#wi-fi-hardening)
6. [🔥 Firewalls & Packet Filtering](#firewalls--packet-filtering)
7. [📊 Traffic Analysis & Surveillance Detection](#traffic-analysis--surveillance-detection)
8. [✅ Operational Checklists](#operational-checklists)
9. [📚 Further Reading](#further-reading)

---

## 🔑 Core Principles

Network OpSec is about controlling **what you reveal** about your communications, location, and behaviour through network traffic.

- 🔐 **Confidentiality** – No one should read your data.
- 🕵️ **Anonymity** – No one should link your traffic to your identity.
- ✅ **Integrity** – No one should tamper with your data in transit.
- 🎭 **Plausible deniability** – Your encrypted traffic could be anything (VPN, Tor, SSH tunnel).

> ⚠️ The adversary can see **metadata**: when you communicate, with whom, how much data, for how long, and from which IP range. That alone is often enough to compromise OpSec.

---

## 🔒 VPNs – Capabilities & Limitations

A VPN (Virtual Private Network) routes your traffic through an encrypted tunnel to a VPN server, then to the destination.

### ✅ What a VPN does well

- 🎭 **Hides your IP** from the destination website or service.
- 🔐 **Encrypts traffic** from your device to the VPN server (protecting against ISP or local network snooping).
- 🌍 **Bypasses geo‑restrictions** and censorship (if the destination trusts the VPN exit IP).

### ❌ What a VPN does NOT do

- 🚫 **Does not make you anonymous** – the VPN provider sees your real IP and can log your activities.
- 🚫 **Does not protect against fingerprinting** – browser fingerprints, cookies, and account logins still identify you.
- 🚫 **Does not hide your traffic from a global adversary** – if the VPN provider is compromised, your logs can be subpoenaed or seized.

### 🎯 VPN selection criteria for OpSec

| Criterion | What to look for |
|-----------|------------------|
| **🌍 Jurisdiction** | Country outside 5/9/14 Eyes, with strong privacy laws (e.g., Switzerland, Iceland, Panama). |
| **📜 Logging policy** | Strict no‑logs (audited, preferably warrant‑canary). No connection logs, no timestamps, no bandwidth logs. |
| **💳 Payment** | Accepts cryptocurrency (Monero preferred), prepaid cards, or cash by mail. |
| **⚙️ Protocol** | WireGuard® (fast, modern), or OpenVPN (widely audited). Avoid PPTP and L2TP/IPsec. |
| **🔌 Kill switch** | Mandatory – blocks all traffic if VPN drops. |
| **🌐 DNS leak protection** | Mandatory – forces DNS through the VPN tunnel. |

### 📝 Best practices

- 🔗 **Do not rely on a single VPN for anonymity** – chain with Tor (VPN → Tor or Tor → VPN) depending on threat model.
- 👥 **Use a dedicated VPN instance** for each persona/compartment (different accounts, different payment methods).
- 🚫 **Never log into real‑identity accounts while VPN is connected** – that links your real identity to the VPN exit IP.
- 🔄 **Rotate VPN servers** – do not always exit from the same city.

---

## 🧅 Tor – The Onion Router

Tor provides stronger anonymity by routing traffic through **three volunteer nodes** (entry, middle, exit), each layer of encryption peeled off one by one.

### 💪 Strengths

- 🔒 **No single point of trust** – only the first node knows your IP, only the last node knows the destination, and they never meet.
- 👥 **Large anonymity set** – millions of users.
- 🕵️ **Hidden services (.onion)** – both client and server are anonymised.
- 🔄 **Stream isolation** – different circuits for different domains.

### ⚠️ Weaknesses & threats

- 👁️ **Exit node sniffing** – The last node can see unencrypted traffic (HTTP, FTP, etc.). Always use HTTPS + Tor.
- 📡 **Correlation attacks** – An adversary who watches both your entry and the exit can correlate timing patterns (requires global passive adversary).
- 😈 **Bad exit nodes** – Malicious exit nodes can inject code or modify content. Use `.onion` when possible.
- 💻 **JavaScript / browser exploits** – Tor Browser disables dangerous features, but zero‑days exist. Keep it updated.

### 🛠️ How to use Tor correctly

| ✅ Do | ❌ Don't |
|----|-------|
| Use **Tor Browser** – not Firefox with Tor proxy. | Log into real‑identity accounts (Facebook, Google, etc.) over Tor – that links your real identity to the Tor exit. |
| Enable **HTTPS Everywhere** and `Security Level: Safest`. | Download files over Tor and open them while online – they may phone home. |
| Use **`.onion` services** whenever available. | Maximize Tor Browser window – screen size is a fingerprinting vector. |
| **New Identity** before switching compartments (Ctrl+Shift+U). | Use Tor for BitTorrent – it harms the network and leaks IP. |
| Run Tor over **VPN** only if you trust the VPN more than your ISP (Tor → VPN hides Tor use from ISP, VPN → Tor hides VPN use from entry node). | Use Tor over public Wi‑Fi without extra protection – still protects, but be aware of local surveillance. |

### 🆚 Tor vs. VPN – Which to use?

| 🎯 Goal | ✅ Recommendation |
|------|----------------|
| Anonymity from websites + strong resistance to surveillance | **🧅 Tor Browser** (with JavaScript disabled) |
| Privacy from ISP / local network + geo‑spoofing | **🔒 VPN** |
| Hiding that you use Tor from your ISP | **🔒 VPN → 🧅 Tor** |
| Accessing a hidden service that requires clearnet interaction | **🧅 Tor → 🔒 VPN** (caution: exit node still sees final destination) |
| Everyday browsing with convenience and moderate privacy | **🔒 VPN** |
| High‑risk activism, whistleblowing, journalism | **🧅 Tor Browser only**, on a dedicated device (e.g., Tails) |

---

## 🌍 DNS & Leak Prevention

DNS (Domain Name System) requests reveal **every domain you visit** – often in plaintext.

### 💧 DNS leaks happen when

- Your VPN kill switch fails.
- Your operating system uses its own DNS (e.g., 8.8.8.8) instead of the VPN’s DNS.
- Your browser uses DoH (DNS over HTTPS) with a different resolver.
- IPv6 is enabled but your VPN only covers IPv4.

### 🛡️ How to prevent leaks

- **Use a VPN with built‑in DNS leak protection** and verify at `ipleak.net`, `dnsleaktest.com`.
- **Disable IPv6** on your network adapter unless your VPN explicitly supports it.
- **Configure your browser to use the system DNS** – disable built‑in DoH unless it points to your VPN’s DNS.
- **Use `dnscrypt-proxy` or `stubby`** for encrypted DNS (DNS over TLS), but ensure it routes through your VPN tunnel.
- **Run a local DNS resolver (Unbound)** that forwards only through the VPN interface.

### 🔍 Testing for DNS leaks

```bash
# Check your visible DNS resolver
dig +short whoami.akamai.net

# Or using online test
curl https://dnsleaktest.com -H "User-Agent: curl"
```

---

## 📡 Wi‑Fi Hardening

Public Wi‑Fi is a hostile environment. Assume every network is monitored.

### ⚠️ Threats on public Wi‑Fi

- 😈 **Evil twin** – Fake AP with same SSID.
- 👤 **Man‑in‑the‑middle** – ARP spoofing, DNS spoofing.
- 🔌 **Deauthentication attacks** – Force you to reconnect to a malicious AP.
- 👁️ **Traffic sniffing** – Unencrypted protocols (HTTP, FTP, Telnet) are fully exposed.

### 🛡️ Defences

- 🔒 **Always use a VPN** – Even on “secure” networks. The VPN tunnel encrypts everything.
- 🧹 **Forget networks after use** – Prevents automatic reconnection to evil twins.
- 📴 **Disable Wi‑Fi when not in use** – Reduces exposure window.
- 🔐 **Use HTTPS + HSTS** – Browser enforces encrypted connections.
- 📱 **Use a personal Wi‑Fi hotspot** – Cellular data + VPN is often safer than public Wi‑Fi.
- 🕵️ **For high‑risk operations, use a Wi‑Fi adapter with monitor mode** – Detect deauth attacks and rogue APs (e.g., `aircrack‑ng`, `Wifite`).

### 🏠 Hardening your own Wi‑Fi (home/office)

| ⚙️ Setting | ✅ Recommendation |
|---------|----------------|
| **🔐 Encryption** | WPA3 (if available) or WPA2‑AES. **Never WEP or WPA‑TKIP**. |
| **📡 SSID broadcast** | Hidden SSID offers minimal security; use strong password instead. |
| **🖥️ Management access** | Disable remote admin, change default credentials. |
| **👥 Guest network** | Isolate IoT devices and guests from your main LAN. |
| **🔄 Firmware** | Update regularly; use OpenWrt or DD‑WRT for advanced control. |
| **🔢 MAC filtering** | Useless against determined attacker; don’t rely on it. |

---

## 🔥 Firewalls & Packet Filtering

A properly configured firewall is your first line of defence against incoming connections and leakage.

### 🐧 Linux (iptables / nftables)

Basic example to block all incoming except established connections:

```bash
# Flush existing rules
sudo iptables -F

# Default policies
sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP
sudo iptables -P OUTPUT ACCEPT   # careful – allow outgoing but may leak

# Allow loopback
sudo iptables -A INPUT -i lo -j ACCEPT

# Allow established connections
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Optional: force all DNS through VPN interface
sudo iptables -A OUTPUT -p udp --dport 53 -j REJECT
```

For a **kill switch** that blocks all non‑VPN traffic:

```bash
# Assuming VPN interface is tun0
sudo iptables -P OUTPUT DROP
sudo iptables -A OUTPUT -o tun0 -j ACCEPT
sudo iptables -A OUTPUT -o lo -j ACCEPT
```

### 🪟 Windows (Windows Defender Firewall)

- Create a rule to block all outbound traffic except to VPN server IP.
- Use `netsh` or GUI to restrict applications to VPN interface only.

### 🍎 macOS (pf)

`/etc/pf.conf` – block all except VPN:

```
block all
pass on lo0
pass on utun0   # typical VPN interface
```

### 📱 Application firewalls

- **Little Snitch** (macOS), **GlassWire** (Windows), **OpenSnitch** (Linux) – monitor outgoing connections per application. Essential for catching leaks.

---

## 📊 Traffic Analysis & Surveillance Detection

Even with VPN/Tor, an adversary can perform **traffic analysis** based on timing, volume, and packet patterns.

### 📈 Observable patterns

- ⏱️ **Packet timing** – Inter‑arrival delays can reveal interactive traffic (SSH, chat) vs. bulk download.
- 📦 **Packet sizes** – Encrypted traffic still leaks length; variable padding helps but is rare.
- 🔢 **Connection counts** – How many concurrent streams, how often, to which ports.
- 🤝 **TLS handshake fingerprints** – JA3 / JA3S signatures can identify the client application.

### 🛡️ Countermeasures

- 🧅 **Use Tor** – It adds random padding and covers timing by multiplexing circuits.
- 🎭 **Use traffic morphing** – Tools like `Obfsproxy`, `Snowflake`, `Meek` disguise Tor traffic as random noise or HTTPS.
- 📡 **Send dummy traffic** – Generate background noise (e.g., `tortunnel`, `hping3`) to make analysis harder.
- ⏳ **Batch your activities** – Instead of browsing continuously, download content and browse offline.

### 🔍 Detecting network surveillance

- **Check for deep packet inspection** – Some ISPs inject headers or reset connections. Use `tcpdump` and compare with expected behaviour.
- **Test for transparent proxies** – Access a non‑existent domain; if you receive a fake response (e.g., ISP ad page), you’re being intercepted.
- **Use `Wireshark` or `tcpdump`** to inspect your own traffic for unexpected connections or DNS queries.

---

## ✅ Operational Checklists

### ✔️ Before connecting to any network

- [ ] 🔒 VPN is running with kill switch enabled.
- [ ] 🌐 IPv6 disabled or VPN tunnel covers it.
- [ ] 🔍 DNS leak test passed (ipleak.net, dnsleaktest.com).
- [ ] 🧅 Browser is in private mode (or Tor Browser).
- [ ] 🚫 No real‑identity accounts are logged in.
- [ ] 🔥 Firewall is configured to drop unexpected outgoing traffic.

### ✔️ When using Tor

- [ ] 🧅 Tor Browser is up‑to‑date.
- [ ] 🔒 Security slider set to “Safest” (JavaScript disabled).
- [ ] 📏 Window is not maximised (default size).
- [ ] 🔌 No plugins, no extra extensions.
- [ ] 🌐 Using `.onion` version of services when available.
- [ ] 🔄 New Identity before switching compartments.

### ✔️ When using public Wi‑Fi

- [ ] ✅ Network is genuine (confirm SSID with staff if possible).
- [ ] 🔒 VPN is connected before opening any browser.
- [ ] 📁 File sharing / AirDrop disabled.
- [ ] 🚫 No automatic login to captive portal using real credentials.
- [ ] 🧹 After session: “Forget this network”.

### ✔️ Periodic checks

- [ ] 📡 Run `tcpdump` or `Wireshark` capture to check for unexpected DNS or traffic outside VPN.
- [ ] 🖥️ Test your browser fingerprint (Panopticlick, AmIUnique).
- [ ] 📜 Review VPN provider’s recent audit and warrant canary.
- [ ] 🔄 Update firewall rules, VPN software, and Tor Browser.

---

## 📚 Further Reading

- **🧅 Tor Project Official Documentation** – [https://community.torproject.org](https://community.torproject.org)
- **🔒 DNS over TLS guide (Stubby)** – [https://dnsprivacy.org](https://dnsprivacy.org)
- **🛡️ EFF’s Surveillance Self‑Defense: Network** – [https://ssd.eff.org/en/module/choosing-vpn-thats-right-you](https://ssd.eff.org/en/module/choosing-vpn-thats-right-you)
- **📖 “Anonymous Communication Networks”** (academic survey)
- **🔥 OpenSnitch (Linux application firewall)** – [https://github.com/evilsocket/opensnitch](https://github.com/evilsocket/opensnitch)
- **🎭 Obfsproxy (Tor pluggable transport)** – [https://github.com/Yawning/obfs4](https://github.com/Yawning/obfs4)

---

*This document is part of the OpSec Documentation Hub. For corrections or additions, please open an issue on GitHub.*
```
