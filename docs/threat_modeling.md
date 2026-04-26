# 🎯 Threat Modeling

[![Documentation](https://img.shields.io/badge/docs-opsec-blue.svg)](./)
[![License](https://img.shields.io/badge/license-CC%20BY--NC--SA%204.0-lightgrey.svg)](LICENSE)
[![Maintenance](https://img.shields.io/badge/maintained%3F-yes-green.svg)](https://github.com/effjy/opsec/graphs/commit-activity)
[![Threats](https://img.shields.io/badge/Threat-Modeling-brightgreen.svg)](https://en.wikipedia.org/wiki/Threat_model)

---

## 📋 Table of Contents

1. [🔑 Core Concepts](#core-concepts)
2. [🏛️ Threat Modeling Frameworks](#threat-modeling-frameworks)
3. [🕵️ Identifying Assets & Adversaries](#identifying-assets--adversaries)
4. [📋 STRIDE Per Threat Category](#stride-per-threat-category)
5. [📊 Risk Assessment](#risk-assessment)
6. [🛡️ Countermeasure Selection](#countermeasure-selection)
7. [🔄 Continuous Review](#continuous-review)
8. [📝 Example Scenarios](#example-scenarios)
9. [✅ Operational Checklists](#operational-checklists)
10. [📚 Further Reading](#further-reading)

---

## 🔑 Core Concepts

Threat modeling is a structured process to identify, quantify, and address security risks. It answers three questions:

1. **What are we protecting?** (Assets)
2. **Who wants to harm us?** (Adversaries)
3. **How could they succeed?** (Threats & vulnerabilities)

> 💡 “You cannot defend against an unknown adversary with an unknown motive using an unknown attack.”

### 🔤 Key terms

- **Asset** – Anything of value: data, reputation, hardware, access.
- **Adversary** – Any entity that may attack: state actor, competitor, hacktivist, insider.
- **Threat** – A potential negative event (e.g., data theft, denial of service).
- **Vulnerability** – A weakness that enables a threat (e.g., unpatched software, weak password).
- **Risk** – Likelihood × Impact of a threat exploiting a vulnerability.
- **Countermeasure** – Control that reduces risk (preventive, detective, corrective).

---

## 🏛️ Threat Modeling Frameworks

Several methodologies exist; choose based on complexity and domain.

### 🔷 STRIDE (Microsoft)

| Acronym | Threat | Example |
|---------|--------|---------|
| **S** | Spoofing | Impersonating a user or device. |
| **T** | Tampering | Modifying data in transit or at rest. |
| **R** | Repudiation | Denying an action (no audit trail). |
| **I** | Information Disclosure | Leaking sensitive data. |
| **D** | Denial of Service | Crashing a service. |
| **E** | Elevation of Privilege | Gaining unauthorised access. |

*Best for software/system design.*

### 🧩 PASTA (Process for Attack Simulation & Threat Analysis)

Seven‑stage, risk‑centric, attacker‑focused. Suitable for high‑risk enterprise.

### 🗺️ OCTAVE (Operationally Critical Threat, Asset, and Vulnerability Evaluation)

Focuses on organisational risk, self‑directed. Good for internal business units.

### ⚡ Trike

Open‑source, uses risk modelling with a “requirements” approach. Produces actionable matrix.

### 📱 LINDDUN

Privacy‑focused extension of STRIDE: Linkability, Identifiability, Non‑repudiation, Detectability, Disclosure, Unawareness, Non‑compliance.

---

## 🕵️ Identifying Assets & Adversaries

### 🏆 Asset inventory

List everything an adversary could want:

- **Personal data** – Identity documents, biometrics, medical records.
- **Credentials** – Passwords, SSH keys, tokens.
- **Financial assets** – Bank accounts, cryptocurrency wallets.
- **Communications** – Email archives, chat logs, call metadata.
- **Physical items** – Laptop, phone, USB drives, printed documents.
- **Reputation** – Social credit, clean record, trust relationships.

> 🔐 For each asset, ask: “What is the worst possible outcome if this is compromised?”

### 👤 Adversary profiling

| Adversary | Motivation | Capabilities | Typical attack |
|-----------|------------|--------------|----------------|
| **Script kiddie** | Notoriety, fun | Low – uses existing tools | Port scans, known exploits. |
| **Cybercriminal** | Financial gain | Medium – custom malware, ransomware | Phishing, credential stuffing. |
| **Hacktivist** | Political statement | Low‑medium – DDoS, doxing | Website defacement, leak documents. |
| **Corporate competitor** | Espionage | Medium‑high | Social engineering, supply chain. |
| **State actor (APTs)** | Strategic intelligence | Very high – zero‑days, custom implants | Long‑term persistence, exfiltration. |
| **Insider (malicious)** | Revenge, greed | High (authorised access) | Data theft, sabotage. |
| **Insider (unwitting)** | None | – | Clicking phishing link, misconfiguration. |

### 🎯 Adversary capability levels

- **Level 0** – Casual adversary, no specialised skills.
- **Level 1** – Uses public tools, follows tutorials.
- **Level 2** – Develops custom scripts, understands vulnerabilities.
- **Level 3** – Employs zero‑day exploits, persistent access.
- **Level 4** – Nation‑state resources, massive infrastructure.

Match your countermeasures to the highest credible adversary level.

---

## 📋 STRIDE Per Threat Category

Evaluate each component of your system against each STRIDE threat.

### 🔹 Spoofing – “Who am I?”

- **Examples** – Forged email, fake login page, MAC address spoofing.
- **Countermeasures** – Multi‑factor authentication, digital signatures, TLS client certificates.

### 🔸 Tampering – “Is my data unchanged?”

- **Examples** – Modifying a configuration file, injecting SQL, altering a binary.
- **Countermeasures** – Integrity checks (hashes, HMAC), signed updates, immutable storage.

### 🔹 Repudiation – “Can they deny it?”

- **Examples** – Deleting logs, using shared accounts, no audit trail.
- **Countermeasures** – Secure logging (append‑only, remote log server), digital signatures on transactions.

### 🔸 Information Disclosure – “Can they read my secrets?”

- **Examples** – Sniffing network, reading unencrypted backup tapes, SQL injection dumping DB.
- **Countermeasures** – Encryption at rest and in transit, access controls, data classification.

### 🔹 Denial of Service – “Can they stop me?”

- **Examples** – Flooding a service, sending malformed packets, exhausting RAM.
- **Countermeasures** – Rate limiting, auto‑scaling, content delivery networks, packet filtering.

### 🔸 Elevation of Privilege – “Can they become admin?”

- **Examples** – Buffer overflow to gain root, exploiting a sudo misconfiguration, session hijacking.
- **Countermeasures** – Minimal privileges, sandboxing, regular patching, privilege separation.

---

## 📊 Risk Assessment

Not all threats are equal. Assign a risk score to prioritise.

### 🔢 Risk formula

> **Risk = Likelihood × Impact**

| Likelihood | Description | Score |
|------------|-------------|-------|
| Rare | Once in several years | 1 |
| Unlikely | Once a year | 2 |
| Possible | Once a quarter | 3 |
| Likely | Once a month | 4 |
| Almost certain | Weekly | 5 |

| Impact | Description | Score |
|--------|-------------|-------|
| Negligible | Minor inconvenience | 1 |
| Low | Small financial loss, temporary disruption | 2 |
| Medium | Several days downtime, data breach of non‑critical info | 3 |
| High | Major reputational damage, data loss of sensitive info | 4 |
| Critical | Financial ruin, physical harm, loss of life | 5 |

### 📈 Risk matrix

| Likelihood \ Impact | 1 | 2 | 3 | 4 | 5 |
|---------------------|---|---|---|---|---|
| 5 | 5 | 10 | 15 | 20 | 25 |
| 4 | 4 | 8  | 12 | 16 | 20 |
| 3 | 3 | 6  | 9  | 12 | 15 |
| 2 | 2 | 4  | 6  | 8  | 10 |
| 1 | 1 | 2  | 3  | 4  | 5  |

- **1‑5** – Accept (monitor)
- **6‑10** – Mitigate (reduce likelihood or impact)
- **12‑20** – High priority; implement strong controls
- **25** – Critical; reassess the operation / stop

---

## 🛡️ Countermeasure Selection

Based on risk score, choose appropriate controls.

### 🛠️ Types of countermeasures

| Type | Example |
|------|---------|
| **Preventive** | Firewall, encryption, access control, training. |
| **Detective** | IDS/IPS, audit logs, integrity checks. |
| **Corrective** | Backup restore, patch management, incident response. |

### 💰 Cost‑benefit consideration

Implement a countermeasure if:

> **(Reduction in risk) × (Asset value) > (Cost of countermeasure)**

### ✅ Defense in depth

Never rely on a single control. Layer them:

- **Physical** – Locks, cameras, security guards.
- **Network** – Firewall, VPN, 802.1X.
- **Host** – Antivirus, HIDS, full disk encryption.
- **Application** – Input validation, authentication.
- **Data** – Encryption, backups, data loss prevention.

---

## 🔄 Continuous Review

Threat modeling is not a one‑time activity. Repeat when:

- **New assets are added** (new service, new data type).
- **Adversary capability changes** (news of new exploit, state‑sponsored campaign).
- **Incident occurs** – Analyse and update model.
- **Periodically** – At least annually for low‑risk, quarterly for high‑risk.

### 📝 Documentation

Maintain a living document with:

- **Asset inventory** (versioned).
- **Data flow diagrams** (DFDs) showing trust boundaries.
- **Threat list** (each with STRIDE category, likelihood, impact, countermeasure).
- **Risk register** – tracked mitigation progress.

---

## 📝 Example Scenarios

### Scenario A: Journalist investigating organised crime

| Asset | Adversary | Threats | Countermeasures |
|-------|-----------|---------|-----------------|
| Contact list | Police / cartel | Interception of phone, seizure of laptop | Signal with disappearing messages, full disk encryption, split contacts across devices. |
| Draft articles | State actor | Hacking into cloud storage | Store only on air‑gapped machine, never in cloud. |
| Identity | Cartel | Doxxing, physical harm | Use pseudonym, separate device for research, burner phone. |

### Scenario B: Remote worker using company laptop

| Asset | Adversary | Threats | Countermeasures |
|-------|-----------|---------|-----------------|
| VPN credentials | Hackers | Phishing, keylogging | Hardware token (YubiKey), password manager. |
| Local projects | Competitor | Theft of laptop | Full disk encryption, BIOS password, remote wipe. |
| Home network | ISP | DNS snooping | Encrypted VPN from laptop, use DoT/DoH. |

### Scenario C: Activist organising protests

| Asset | Adversary | Threats | Countermeasures |
|-------|-----------|---------|-----------------|
| Meeting locations | Law enforcement | Traffic analysis, undercover attendance | Use encrypted messaging (Signal), change meeting spots. |
| Participant list | Malicious actor | Phone number leak | Use anonymous sign‑up (SimpleX, anonymous temporary emails). |
| Physical safety | Violent opposition | Identification at protest | Wear generic clothing, mask, disable phone location. |

---

## ✅ Operational Checklists

### ✔️ Initial threat model creation

- [ ] **Identify assets** – List all valuable data and devices.
- [ ] **Identify adversaries** – Who would want those assets? (Even plausibly.)
- [ ] **Map data flows** – Where does data go? (Local, network, cloud, USB.)
- [ ] **Apply STRIDE** – For each flow, list plausible threats.
- [ ] **Assess risk** – Likelihood × Impact → score.
- [ ] **Document countermeasures** – Existing and needed.
- [ ] **Create risk register** – Assign owners and deadlines.

### ✔️ Monthly / quarterly review

- [ ] New assets or data types added?
- [ ] Changes in adversary capability (news, alerts)?
- [ ] Any security incidents or near misses?
- [ ] Are countermeasures still effective? (Test if possible.)
- [ ] Update risk scores based on new information.

### ✔️ Before a high‑risk operation

- [ ] Perform **lean threat model** (1 hour) focused on that operation.
- [ ] Identify the **single most likely threat** and have a specific countermeasure.
- [ ] Plan for **worst‑case compromise** (how to burn, escape, contain).
- [ ] **Rehearse** the operation with the threat model in mind.

---

## 📚 Further Reading

- **📘 Threat Modeling: Designing for Security (Adam Shostack)** – The canonical book.
- **🛡️ OWASP Threat Modeling** – [https://owasp.org/www-community/Threat_Modeling](https://owasp.org/www-community/Threat_Modeling)
- **📊 Microsoft STRIDE & DFD** – [https://learn.microsoft.com/en-us/security/engineering/threat-modeling-tool](https://learn.microsoft.com/en-us/security/engineering/threat-modeling-tool)
- **📑 NIST SP 800‑30 Rev. 1 (Risk Assessment)** – [https://csrc.nist.gov/publications/detail/sp/800-30/rev-1/final](https://csrc.nist.gov/publications/detail/sp/800-30/rev-1/final)
- **🎯 LINDDUN Privacy Framework** – [https://linddun.org](https://linddun.org)

---

*This document is part of the OpSec Documentation Hub. For corrections or additions, please open an issue on GitHub.*
```
