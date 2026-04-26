# Identity & Anonymity

## Table of Contents

1. [Core Concepts](#core-concepts)
2. [Pseudonyms & Personas](#pseudonyms--personas)
3. [Compartmentation](#compartmentation)
4. [Metadata & Digital Footprints](#metadata--digital-footprints)
5. [Anonymizing Networks](#anonymizing-networks)
6. [Browser & Device Fingerprinting](#browser--device-fingerprinting)
7. [Operational Security Checklists](#operational-security-checklists)
8. [Common Pitfalls & Real-World Cases](#common-pitfalls--real-world-cases)
9. [Further Reading](#further-reading)

---

## Core Concepts

**Identity** in OpSec is not a single attribute—it is a collection of signals that uniquely or semi‑uniquely point to a person, device, or behavior.  
**Anonymity** means operating without a verifiable link to your real identity. **Pseudonymity** means using a persistent alternate name that may still have links to your true self.

- **Identifier**: Any piece of data that can distinguish an entity (e.g., name, email, IP address, device serial number, typing pattern).
- **Anonymity set**: The group of possible subjects among which an observer cannot distinguish you. Larger sets → stronger anonymity.
- **Linkability**: The ability to connect two or more actions, identities, or pieces of information to the same entity.

> “Anonymity is not a binary state; it is a spectrum that depends on the adversary’s capabilities and the context.”

---

## Pseudonyms & Personas

A *pseudonym* is a name you use consistently across certain contexts. A *persona* is a full character – with its own backstory, interests, and even writing style.

### Best practices

- **Never reuse a pseudonym across different security domains** (e.g., writing blog posts and signing up for a forum about the same hobby – those two can be linked by language analysis).
- **Create isolation by platform**: one pseudonym for GitHub, another for Reddit, a third for Telegram.
- **Use persona‑appropriate details**: fake birthday, fake location, fake “about me” text. These details should be consistent across all accounts tied to that persona.
- **Consider using `anonaddy`, `simplelogin` or similar email aliasing** to generate disposable, forward‑only addresses per pseudonym. Do not use the same real email to register multiple pseudonyms – that creates a cross‑link.

### Persona lifecycle

| Phase | Action |
|-------|--------|
| **Creation** | Decide purpose, threat model, needed longevity. |
| **Seeding** | Register accounts, post harmless content, build natural history. |
| **Operation** | Stick to the persona’s defined boundaries. Never break compartmentation. |
| **Burn** | When compromised or no longer needed – delete all accounts, overwrite data, abandon entirely. |

---

## Compartmentation

Compartmentation is the separation of identities, devices, networks, and even lifestyles so that a breach in one compartment does not affect the others.

### Physical compartments

- **Dedicated devices** – One laptop for “Work”, one for “Personal”, one for “Alternative identity”. Never mix them.
- **Virtual machines** – Isolate activities on the same physical machine using VMs (e.g., VirtualBox, Qubes OS). Each VM belongs to one compartment.
- **Live USB / Tails** – For extremely sensitive operations, boot into a temporary operating system (Tails) that leaves no trace.

### Digital compartments

- **Separate browsers/profiles** – Use Firefox containers, Chrome profiles, or even different browsers for different personas.
- **Different email providers** – Do not recover a pseudonym’s email account using your real phone number or backup email.
- **Separate cloud storage** – One Drive/Dropbox account per persona, never cross‑logged‑in.

### Operational compartmentation

- **Time separation** – Perform activities for Persona A on Tuesdays, Persona B on Thursdays.
- **Network separation** – Use different VPN exit nodes, or Tor circuits, for each compartment. Re‑use of the same exit node can link sessions.
- **Language & writing style** – Change vocabulary, punctuation, and even typical typos between personas.

---

## Metadata & Digital Footprints

**Metadata is often more revealing than content.** Every file, message, and online action leaks metadata.

### Common metadata types

| Metadata | Leaks | Mitigation |
|----------|-------|-------------|
| File Exif | GPS, camera model, timestamps, software version | Strip with `exiftool`, `mat2` |
| Email headers | Sender IP, client, routing hops | Use webmail over Tor, or remailers |
| Document properties | Author name, company, edit time | Save as plain text, then re‑export |
| Code commits | User.name, user.email, timezone | `git config --global user.name "Pseudonym"` |
| Social media | "Liked by…", geotags, friend networks | Post from isolated browser, disable all metadata, use fake check‑ins |

### How to reduce your digital footprint

- **Minimize accounts** – Each extra account is a potential link.
- **Use burner services** – Temporary phone numbers, disposable email addresses, short‑lived file uploads.
- **Log out** – Do not stay logged into multiple identities on the same browser session.
- **Clear state** – Regularly delete cookies, local storage, browser cache, and history. Even better – use ephemeral browser profiles (e.g., `firefox -CreateProfile`, then delete after closing).

---

## Anonymizing Networks

The network layer is where many anonymity attempts fail.

### VPNs

- VPNs **hide your IP from the destination** but **trust the VPN provider**.
- VPNs **do not make you anonymous** – they shift trust to the VPN operator.
- Use VPNs **only when authenticated to known services**, or as a first hop before Tor.

### Tor

- **Tor** (The Onion Router) provides stronger anonymity by routing traffic through three volunteer nodes.
- **Never log into real‑identity accounts over Tor** – that links your real identity to the Tor exit node (and all other sessions using that exit).
- Use **Tor Browser** – it forces a consistent, fingerprint‑resistant configuration.
- **Stream isolation** – Different tabs in Tor Browser use different circuits. Use separate browser instances for different personas.

### I2P

- **I2P** is designed for internal “hidden” services, not for accessing the clearnet. It offers stronger resistance to traffic correlation within the I2P network.
- Use I2P for hosting anonymous websites (eepsites) and for applications like BitTorrent.

### Which to choose?

| Use case | Recommended |
|----------|-------------|
| Browsing sensitive information | Tor Browser |
| Hiding BitTorrent traffic | VPN (with no‑logs) or I2P |
| High‑latency, asynchronous messages | Mix networks (e.g., Mixmaster, Nym) |
| Accessing onion services | Tor |
| Everyday privacy from ISP | VPN |

---

## Browser & Device Fingerprinting

Even with a VPN or Tor, websites can identify you through **fingerprinting** – collecting a set of browser and device attributes that together form a nearly unique “fingerprint”.

### Key fingerprinting vectors

- **User‑Agent** – Browser version, OS, CPU architecture.
- **Screen resolution & color depth**.
- **Installed fonts** – As revealed via JavaScript.
- **Canvas & WebGL fingerprinting** – Graphics card behaviour.
- **Time zone & language**.
- **Installed browser extensions** (some can be detected).
- **WebRTC leaks** – Real IP may be exposed even behind a VPN (disable WebRTC or use `uBlock Origin`).
- **Media devices** – List of microphones/cameras.

### Mitigations

- **Use Tor Browser** – It blocks most fingerprinting vectors and returns identical values for all users (maximum fingerprint entropy = 0).
- **For Firefox:** Set `privacy.resistFingerprinting = true` in `about:config`. Use `CanvasBlocker` add‑on.
- **For Chromium browsers:** Use `Brave` with strict fingerprinting protection, or `Ungoogled Chromium`.
- **Disable JavaScript** when possible (NoScript, uMatrix). JavaScript is the primary vehicle for fingerprinting.
- **Run a script like `rpi-fingerprint` or `browser-fingerprinting-check`** to audit your own fingerprint uniqueness.

---

## Operational Security Checklists

### ✔️ Before creating a new identity

- [ ] Define the persona’s purpose (temporary / long‑term).
- [ ] Choose a dedicated device or VM.
- [ ] Set a fixed time window and network path (e.g., via Tor).
- [ ] Create a new email address (protonmail, tutanota) **over Tor**.
- [ ] Register accounts using that email.
- [ ] Seed historical content – join public groups, post harmless messages.
- [ ] Document persona details in an encrypted container (e.g., Veracrypt).
- [ ] Do **not** use the persona to log into any real‑identity service.

### ✔️ During operation

- [ ] Never reuse a password between personas.
- [ ] Avoid associating the persona with real‑world events that are also recorded elsewhere.
- [ ] Do not post the same piece of text across multiple personas – stylometry can link them.
- [ ] Never log into the persona’s accounts from an unmasked device.
- [ ] Regularly rotate user‑agents and circuits (in Tor: `New Identity`).

### ✔️ When retiring a persona

- [ ] Delete all online accounts (follow each platform’s deletion procedure).
- [ ] Overwrite any local files or key material.
- [ ] Stop using that persona’s email address; let it expire.
- [ ] If possible, post a “goodbye” message from all accounts before deletion (to explain disappearance).
- [ ] Burn the VM or clean the Tails USB.

---

## Common Pitfalls & Real‑World Cases

### Pitfall #1: Using the same email recovery options

You create a pseudonym’s email. Later you forget the password and click “recover via phone number”. That phone number is tied to your real identity. **Now the pseudonym is linked forever.**

**Fix:** Never set a recovery method. Or use a burner phone number (e.g., from a SMS‑receive service) and abandon it after registration.

### Pitfall #2: Posting from the same public Wi‑Fi at the same hours

Even if you use a VPN, if you always post from “Starbucks on Main Street” at 8:05 AM, a motivated adversary can correlate time/location to video surveillance or other logs.

**Fix:** Vary your access points and times. Use Tor over cellular hotspots, or connect from different cities when possible.

### Pitfall #3: Writing style fingerprinting

A unique turn of phrase, favourite emoji, or consistent misspelling (e.g., “teh” instead of “the”) can be used to link accounts even if everything else is isolated.

**Fix:** For high‑stakes personas, adopt a “persona grammar” – use British vs. American spelling, change punctuation, add or remove spaces around dashes. Or use a text transformation tool (e.g., `anonymouth` for stylometry‑aware rewriting).

### Real‑world case: The Silk Road

The operator of Silk Road, “Dread Pirate Roberts”, was identified partly because **he reused a pseudonym (“altoid”) across multiple forums** and made the mistake of linking a personal email address during the early days of the site. This set of cross‑forum posts allowed the FBI to connect his pseudonymous identity to his real‑world identity.

**Lesson:** Even minor cross‑compartment slips can be aggregated by a persistent adversary.

---

## Further Reading

- **Tor Project Official Documentation** – [https://community.torproject.org/](https://community.torproject.org/)
- **“How to Disappear” by Frank Ahearn** (outdated but valuable for mindset)
- **Qubes OS Documentation** – Compartmentation via virtualization.
- **Panopticlick by EFF** – Test your browser fingerprint: [https://panopticlick.eff.org](https://panopticlick.eff.org)
- **“Anonymity Loves Company: Usability and the Network Effect”** (academic paper)
- **EFF’s Surveillance Self‑Defense** – [https://ssd.eff.org](https://ssd.eff.org)

---

*This document is part of the OpSec Documentation Hub. For corrections or additions, please open an issue on GitHub.*
```
