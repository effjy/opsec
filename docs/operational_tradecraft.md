# 🕵️ Operational Tradecraft

[![Documentation](https://img.shields.io/badge/docs-opsec-blue.svg)](./)
[![License](https://img.shields.io/badge/license-CC%20BY--NC--SA%204.0-lightgrey.svg)](LICENSE)
[![Maintenance](https://img.shields.io/badge/maintained%3F-yes-green.svg)](https://github.com/effjy/opsec/graphs/commit-activity)
[![Tradecraft](https://img.shields.io/badge/Tradecraft-Essentials-brightgreen.svg)](https://en.wikipedia.org/wiki/Tradecraft)

---

## 📋 Table of Contents

1. [🔑 Core Principles](#core-principles)
2. [🎭 Cover & Legend Development](#cover--legend-development)
3. [📱 Secure Communications](#secure-communications)
4. [🔄 Dead Drops & Cut-Outs](#dead-drops--cut-outs)
5. [📦 Secure File Transfer & Storage](#secure-file-transfer--storage)
6. [🔐 Meeting & Recognition Techniques](#meeting--recognition-techniques)
7. [🚨 Countersurveillance & Blowback](#countersurveillance--blowback)
8. [🔥 Burn & Escape Procedures](#burn--escape-procedures)
9. [✅ Operational Checklists](#operational-checklists)
10. [📚 Further Reading](#further-reading)

---

## 🔑 Core Principles

Operational tradecraft is the art of conducting activities without revealing intent, identity, or methods. It is rooted in centuries of espionage and adapted for digital resistance.

- 🎭 **Plausible deniability** – Every action should have a benign explanation.
- 📂 **Compartmentation** – No single person knows the whole picture.
- 🔄 **Need‑to‑know** – Information is shared only with those who require it.
- 📅 **Operational security (OPSEC) lifecycle** – Identify critical information, analyse threats, assess vulnerabilities, apply countermeasures, and rehearse.
- 🕰️ **Dead drops & cut‑outs** – Indirect communication breaks links.

> 💡 “Tradecraft is not about being invisible; it’s about being unremarkable.”

---

## 🎭 Cover & Legend Development

A **cover** is the false identity used during an operation. A **legend** is the backstory that supports that cover.

### 📝 Cover types

| Type | Description | Examples |
|------|-------------|----------|
| **Official cover** | Backed by a real organisation (e.g., diplomat, NGO worker). | “Consultant for a logistical firm.” |
| **Unofficial cover (NOC)** | No organisational backing – more dangerous. | “Freelance journalist,” “tourist.” |
| **Light cover** | Minimal legend, used for short operations. | “Student,” “software engineer.” |

### 🛠️ Building a credible legend

- **Depth** – Know your fictitious name, birthdate, hometown, family structure, education, workplace.
- **Documentation** – Carry plausible IDs, business cards, receipts, social media presence.
- **Consistency** – Stick to the same details across all interactions.
- **Exercise** – Role‑play with a trusted friend to find gaps.

### 🧹 Cover maintenance

- **Regularly update** – Change phone numbers, email addresses, social profiles periodically.
- **Use cover‑specific devices** – Never mix with real identity.
- **Burn after use** – If a cover is exposed, abandon it entirely.

---

## 📱 Secure Communications

Communication is the most vulnerable phase of any operation.

### 📲 Messaging & voice

| Tool | Security | Notes |
|------|----------|-------|
| **Signal** | End‑to‑end encrypted + metadata protection | ✅ Recommended for daily use. |
| **Wire** | E2EE, open source | Good for group chats. |
| **SimpleX** | No user identifiers, E2EE | Highest anonymity, no phone number required. |
| **Session** | Onion routing, E2EE | No metadata, uses public keys. |
| **Briar** | Peer‑to‑peer, Tor | Works offline via Bluetooth/Wi‑Fi. |

> ⚠️ **Never use SMS or regular phone calls for sensitive information.** They are not encrypted and are logged by carriers.

### 📧 Email best practices

- Use **ProtonMail**, **Tutanota**, or **Mailfence** – E2EE, no IP logging, based in privacy‑friendly jurisdictions.
- **Never** use the same email across multiple personas.
- **Disable remote images** – They can track you via web bugs.
- **Set a very long, random password + 2FA** (hardware key preferred).
- **Encrypt email content** with PGP/GPG if the provider doesn’t offer E2EE.

### ⏱️ Timing & patterns

- **Avoid fixed schedules** – Stagger communication times.
- **Use dropbox messaging** (e.g., `protectedtext.com` over Tor) for asynchronous messages.
- **Never acknowledge receipt** in ways that could be recorded (e.g., “got your message”).

---

## 🔄 Dead Drops & Cut‑Outs

**Dead drops** are physical or digital locations where information is left for another party without direct contact. **Cut‑outs** are intermediaries who relay messages, breaking the direct link between operatives.

### 📦 Physical dead drops

- **Classic**: Behind loose brick, under park bench, inside hollowed book in a public library.
- **Magnetic** – Hide a USB drive under a metal surface (e.g., traffic light box).
- **Geocaching** – Use existing geocache containers (but be aware of public logs).

### 🌐 Digital dead drops

- **Password‑protected pastebins** (e.g., `privatebin.net` over Tor).
- **Encrypted file on a public cloud** (e.g., ProtonDrive, shared link via different channel).
- **Data hidden in online images** (steganography) – embed message into a casual photo uploaded to social media.

### 🔁 Using a cut‑out

1. Operative A tells cut‑out a message.
2. Cut‑out meets operative B and passes the message.
3. Cut‑out knows both, but A and B never meet or know each other’s identity.

> 🔐 **Never rely on a single cut‑out for multiple operations** – they become a single point of failure.

---

## 📦 Secure File Transfer & Storage

Moving files without leaving traces requires encryption and indirect channels.

### 📂 Encryption before transfer

- **VeraCrypt container** – Mount and drop files, then unmount.
- **Age** (`age` tool) – Simple, modern file encryption.
- **GnuPG** `gpg -c file` (symmetric) or `gpg -e -r recipient` (public key).

### 🚚 Transfer methods

| Method | Security | Notes |
|--------|----------|-------|
| **USB dead drop** | High (physical) | Use encrypted drive, wipe after. |
| **Wi‑Fi file transfer (Magic Wormhole)** | Medium | Generates one‑time code, encrypted channel. |
| **OnionShare** | High | Share files over Tor, access via .onion address. |
| **Burner cloud accounts** | Medium | Create anonymous account via Tor, share link encrypted. |
| **CD/DVD in mail** | Physical | Use pseudonym and drop address. |

### 🗄️ Long‑term storage

- **Encrypted containers on cloud** – Protect with strong passphrase, store only encrypted blobs.
- **Physical safes** – For hardware media, use fire‑resistant safe with combination lock.
- **Shamir’s Secret Sharing** – Split file into N shares, require K shares to reconstruct. Protects against loss or compromise.

---

## 🔐 Meeting & Recognition Techniques

When physical meetings are unavoidable, tradecraft reduces risk.

### 👀 Recognition signals

- **Pre‑arranged ‘spontaneous’ cues** – e.g., tying a shoelace, adjusting glasses, carrying a specific coloured bag.
- **Duress signal** – If forced, use a signal (e.g., wrong handshake, reversed button) to warn the other party.
- **Two‑step authentication** – First signal indicates “ready”, second confirms “safe”.

### 📍 Meeting locations

- **Casual, high‑traffic areas** – Coffee shops, public parks, museums, sports events.
- **Avoid CCTV** – Check for cameras; use maps like `Overpass Turbo` to find blind spots.
- **Establish escape routes** – Know at least two exits.
- **Time‑limited** – Keep meetings short (under 15 minutes).

### 🕵️ Counter‑surveillance during meet

- **Arrive early** – Observe the area, look for loiterers, repeated vehicles.
- **Use ‘SDR’ (surveillance detection route)** – Change direction, double back, enter/exit buildings.
- **Signal compromise** – If you suspect surveillance, abort the meeting using a prearranged code (e.g., text “Sorry, running late” means abort).

---

## 🚨 Countersurveillance & Blowback

**Countersurveillance** identifies if you are being watched. **Blowback** refers to unintended consequences that affect you or your network.

### 🔍 Simple detection techniques

- **Mirroring** – Walk in a circle; if the same person follows, you have a tail.
- **The ‘dead drop’ of surveillance** – Stop near a reflective surface (window, car) and glance back.
- **Using public transport** – Board a bus/train, then get off just before it departs; see who also quickly exits.
- **Listening for radio chatter** – Unusual static or earpiece adjustment can indicate a surveillance team.

### 🛑 When you suspect surveillance

- **Do nothing unusual** – Continue your routine.
- **Abort any sensitive activity** – Do not proceed to a dead drop or meeting.
- **Go to a safe location** – Hotel lobby, shopping centre, police station (if not the adversary).
- **Wait it out** – Surveillance may give up after some time.

### 💥 Blowback mitigation

- **Assume everything is compromised** – Once a link is exposed, treat the entire compartment as burned.
- **Have a ‘fire plan’** – Pre‑written script for plausible denial, backup identities, emergency funds.
- **Destroy evidence** – Burn documents, wipe devices, destroy SIMs.

---

## 🔥 Burn & Escape Procedures

A **burn** is when an identity, location, or method becomes known to the adversary.

### 🔥 Immediate actions after a burn

1. **Cut all contact** – Do not attempt to warn others directly (that may lead them to you).
2. **Use a dead drop to send a ‘burn notice’** (coded message) to your network.
3. **Abandon safe houses, email accounts, phone numbers, social media**.
4. **Use a pre‑planned escape kit** – Cash, clothes, false IDs, encrypted USB, basic medical supplies.
5. **Move to a secondary safe location** (never the primary).
6. **Go dark** – No electronic activity that can be correlated.

### 🏃 Escape routes

- **Pre‑planned hide sites** – Trusted friend’s house (who is not in your network), hotel paid with cash, camping site.
- **Cross‑border escape** – Use official border crossing with a clean passport, or illegal crossing only if trained.
- **Use ‘untraceable’ transportation** – Hitchhiking, bicycle, taxi paid with cash, stolen car (risk trade‑off).

### 📦 Burn bag contents (keep ready)

- 💵 Cash (various denominations, local currency + USD/EUR).
- 📱 Dumb phone (no accounts, pre‑paid SIM).
- 🆔 Fake ID (plausible quality, different name).
- 🔑 Keys to a secondary safe house.
- 💊 Basic medications + first‑aid kit.
- 👕 Change of clothes (generic style).
- 📝 Micro‑SD card with essential contacts (encrypted).

---

## ✅ Operational Checklists

### ✔️ Before an operation (any type)

- [ ] **Define objective** – What exactly must be achieved?
- [ ] **Threat model** – Who could be watching? What are their capabilities?
- [ ] **Cover story** – Full legend, practice answering questions.
- [ ] **Communications plan** – Channels, timings, emergency abort signal.
- [ ] **Destruction plan** – How to destroy evidence if compromised.
- [ ] **Escape route** – Minimum two, rehearsed.
- [ ] **Burn bag** – Checked and accessible.
- [ ] **Surveillance detection route** – Planned and varied.

### ✔️ During a physical meeting

- [ ] Arrive early, observe.
- [ ] Perform SDR (surveillance detection route) en route.
- [ ] Use pre‑arranged recognition signal.
- [ ] Keep meeting brief (<15 min).
- [ ] Speak in neutral language, no names.
- [ ] Leave separately, via different exits.
- [ ] Perform post‑meeting SDR.

### ✔️ After an operation (debrief)

- [ ] Write a **cryptographically encrypted log** (date, participants, outcomes, anomalies).
- [ ] Review what could have been observed (CCTV, witnesses, mobile signals).
- [ ] Rotate communication channels and meeting sites.
- [ ] If any irregularity, consider the operation **burned** and adjust future plans.

### ✔️ Monthly / quarterly tradecraft maintenance

- [ ] Review and update legends (new details, fresh social media).
- [ ] Replace dead drop locations (burn the old ones).
- [ ] Change encryption keys and passwords.
- [ ] Practice SDR with a trusted friend.
- [ ] Update burn bag (cash not expired, clothes fit, device charged).

---

## 📚 Further Reading

- **📖 “The Official CIA Manual of Trickery and Deception”** (historical, but foundational).
- **📕 “The Art of Deception” by Kevin Mitnick** (social engineering).
- **📘 “Intelligence Tradecraft” (DIA Job Aid)** – declassified techniques.
- **🌐 Surveillance Self‑Defense (EFF)** – [https://ssd.eff.org](https://ssd.eff.org)
- **🧅 OnionShare** – [https://onionshare.org](https://onionshare.org)
- **📂 Magic Wormhole** – [https://magic-wormhole.readthedocs.io](https://magic-wormhole.readthedocs.io)

---

*This document is part of the OpSec Documentation Hub. For corrections or additions, please open an issue on GitHub.*
```
