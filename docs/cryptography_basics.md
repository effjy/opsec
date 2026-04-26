# 🔐 Cryptography Basics

[![Documentation](https://img.shields.io/badge/docs-opsec-blue.svg)](./)
[![License](https://img.shields.io/badge/license-CC%20BY--NC--SA%204.0-lightgrey.svg)](LICENSE)
[![Maintenance](https://img.shields.io/badge/maintained%3F-yes-green.svg)](https://github.com/effjy/opsec/graphs/commit-activity)
[![Crypto](https://img.shields.io/badge/Cryptography-101-brightgreen.svg)](https://en.wikipedia.org/wiki/Cryptography)

---

## 📋 Table of Contents

1. [🔑 Core Concepts](#core-concepts)
2. [🔒 Symmetric Encryption](#symmetric-encryption)
3. [🔓 Asymmetric Encryption (Public Key)](#asymmetric-encryption-public-key)
4.  [🔢 Hash Functions](#hash-functions)
5. [🔐 Key Management & Derivation](#key-management--derivation)
6. [📦 Authenticated Encryption (AEAD)](#authenticated-encryption-aead)
7. [🔄 Hybrid Cryptosystems](#hybrid-cryptosystems)
8. [⚠️ Common Pitfalls & Misconceptions](#common-pitfalls--misconceptions)
9. [✅ Operational Checklists](#operational-checklists)
10. [📚 Further Reading](#further-reading)

---

## 🔑 Core Concepts

Cryptography is the science of securing communication by transforming plaintext into ciphertext using a key. It provides several properties:

- 🔒 **Confidentiality** – Only authorised parties can read the data.
- ✅ **Integrity** – Data has not been altered in transit.
- 🆔 **Authentication** – The sender is who they claim to be.
- 🚫 **Non‑repudiation** – The sender cannot deny having sent the message.

> 💡 “Cryptography is about protecting information, not hiding its existence – that’s steganography.”

---

## 🔒 Symmetric Encryption

In symmetric encryption, the **same key** is used for both encryption and decryption. It is fast and suitable for bulk data.

### 🔑 Common symmetric algorithms

| Algorithm | Key size | Block size | Notes |
|-----------|----------|------------|-------|
| **AES** | 128, 192, 256 bits | 128 bits | NIST standard, widely audited. Use AES‑256 for high security. |
| **ChaCha20** | 256 bits | stream cipher | Fast in software, no hardware acceleration needed. Used in TLS 1.3. |
| **Serpent** | 128–256 bits | 128 bits | More conservative, slower; highly secure. |
| **3DES** | 168 bits | 64 bits | Deprecated, weak against meet‑in‑the‑middle. **Do not use**. |

### ⚙️ Modes of operation

Block ciphers like AES need a **mode** to encrypt data longer than one block.

| Mode | Type | Security | Recommendation |
|------|------|----------|----------------|
| **GCM** | Authenticated (AEAD) | Strong with unique IVs | **Recommended** – provides encryption + integrity. |
| **CTR** | Stream | Good with unique IVs | Use only with a MAC for integrity. |
| **CBC** | Block | Use with HMAC | Padding oracle risks if not careful. |
| **ECB** | Block | **Broken** | Never use – patterns remain. |

> ⚠️ **Never reuse the same IV/nonce with the same key in GCM, CTR, or ChaCha20. It breaks confidentiality catastrophically.**

### 📝 Best practices

- ✅ Use **AES‑256‑GCM** or **ChaCha20‑Poly1305** for most applications.
- ✅ Generate IVs/nonces randomly (minimum 12 bytes) or use a counter.
- ✅ Authenticate your ciphertext (GCM or separate HMAC) to prevent tampering.
- ❌ Never roll your own crypto – use standard, audited libraries.

---

## 🔓 Asymmetric Encryption (Public Key)

Asymmetric encryption uses a **key pair**: public key (encrypt) and private key (decrypt). It is slow and typically used for key exchange or digital signatures.

### 🔑 Common algorithms

| Algorithm | Use | Key sizes | Notes |
|-----------|-----|-----------|-------|
| **RSA** | Encryption, signatures | 2048–4096 bits | Widely used but vulnerable to quantum attacks. Use OAEP padding. |
| **ECC (ECDH, ECDSA)** | Key exchange, signatures | 256–521 bits | Smaller keys, faster than RSA. |
| **X25519 / Ed25519** | Key exchange, signatures | 256 bits | Modern, safe defaults (Bernstein). |
| **Kyber** | Post‑quantum KEM | 512–1024 variants | NIST standard (FIPS 203). Resists quantum attacks. |

### 📜 When to use asymmetric crypto

- 🔑 **Key exchange** – Establish a shared secret over an insecure channel (e.g., Diffie‑Hellman, ECDH, Kyber).
- ✍️ **Digital signatures** – Prove authenticity and integrity of a message (RSA‑PSS, ECDSA, Ed25519).
- 📬 **Encrypting small messages** (e.g., encrypting a symmetric key for a recipient).

> ❌ Never encrypt large files directly with RSA – it’s too slow and handles only small amounts of data.

---

## 🔢 Hash Functions

A hash function takes any input and produces a fixed‑size digest. It is **one‑way** and **collision‑resistant**.

### 🔑 Common hash algorithms

| Algorithm | Output size | Security | Notes |
|-----------|-------------|----------|-------|
| **SHA3‑256/512** | 256/512 bits | High | NIST standard, sponge construction. |
| **SHA‑256** | 256 bits | Good, but length‑extension attacks exist. Use SHA‑3 or SHA‑512/256. |
| **BLAKE2b** | 256/512 bits | High | Faster than SHA‑3, used in many projects. |
| **MD5 / SHA‑1** | 128/160 bits | **Broken** | Never use for security. |

### 🛡️ Uses of hashing

- **Password hashing** – Not directly! Use slow KDFs (Argon2id, bcrypt, PBKDF2) instead.
- **Integrity verification** – Compare hash of downloaded file with trusted source (e.g., `sha256sum file`).
- **Commitments** – Commit to a value without revealing it (hash then reveal).
- **Merkle trees** – Used in blockchains and git.

> ⚠️ **Do not store passwords with plain hash functions** – use a dedicated password hashing algorithm with salt and work factor.

---

## 🔐 Key Management & Derivation

Keys are the most critical part of any cryptosystem. Protect them like the data itself.

### 🗝️ Key generation

- **Use a cryptographically secure random number generator** (CSPRNG) – `/dev/urandom` on Linux, `BCryptGenRandom` on Windows.
- **Enough entropy** – For symmetric keys, 256 bits is sufficient. For RSA, 3072 or 4096 bits.

### 🔑 Key derivation functions (KDFs)

KDFs turn a password or low‑entropy secret into a cryptographically strong key.

| KDF | Properties | Recommended |
|-----|------------|-------------|
| **Argon2id** | Memory‑hard, side‑channel resistant | ✅ **Best for passwords** (default: Argon2id, 1‑4 threads, 512MB). |
| **bcrypt** | Adaptive, cost factor | ✅ Good, resistant to GPU cracking. |
| **PBKDF2** | Iteration‑based, FIPS certified | ✅ Acceptable but not memory‑hard; use with high iteration count (≥600k). |
| **scrypt** | Memory‑hard | ✅ Good alternative to Argon2. |

### 🔁 Key rotation & storage

- **Rotate keys** periodically (e.g., every 1–2 years for long‑term encryption keys).
- **Store private keys in encrypted form** (e.g., password‑protected PKCS#8, or hardware security module).
- **Use hardware security modules (HSM), TPM, or smart cards** for high‑security key storage.
- **Backup keys securely** – split via Shamir’s Secret Sharing or store in multiple safe locations.

---

## 📦 Authenticated Encryption (AEAD)

Authenticated encryption ensures **both confidentiality and integrity** in one operation. Attackers cannot modify ciphertext without detection.

### 🛠️ Common AEAD modes

- **AES‑GCM** – Most common. Requires unique nonce per key (12 bytes).
- **ChaCha20‑Poly1305** – Fast in software, also requires unique nonce.
- **AES‑CCM** – Less common, used in constrained environments.

### 🧪 Why AEAD matters

Without authentication, an attacker can tamper with ciphertext:

- **Bit flipping** – Change ciphertext bytes, resulting in predictable plaintext changes.
- **Padding oracles** – CBC mode + padding can leak plaintext bit by bit.
- **Replay attacks** – Old valid ciphertexts can be resent unless freshness is checked.

> ✅ **Always use AEAD (e.g., AES‑256‑GCM or ChaCha20‑Poly1305) when encrypting data.**

---

## 🔄 Hybrid Cryptosystems

Most real‑world systems combine symmetric and asymmetric encryption.

### Example: PGP / GPG

1. Generate a random **session key** (symmetric).
2. Encrypt the message **symmetric** (fast, efficient).
3. Encrypt the session key with the **recipient’s public key** (asymmetric).
4. Send both the encrypted session key and the ciphertext.

### Example: TLS 1.3

- Asymmetric (ECDHE, Kyber) for key exchange.
- Symmetric (AES‑GCM or ChaCha20‑Poly1305) for bulk data encryption.
- Hash (SHA‑256/384) for transcript hash and handshake integrity.

> 🧠 **Hybrid crypto gives you the speed of symmetric with the key distribution benefits of asymmetric.**

---

## ⚠️ Common Pitfalls & Misconceptions

### 🔴 Mistake #1: Using ECB mode

ECB mode encrypts each block independently → repeated plaintext blocks produce identical ciphertext blocks. **Never use ECB.**

### 🔴 Mistake #2: Reusing nonces/IVs

Reusing a nonce with the same key in GCM, CTR, or ChaCha20 destroys security – an attacker can recover the keystream.

### 🔴 Mistake #3: Rolling your own crypto

Even experts get it wrong. Use standard, peer‑reviewed libraries (libsodium, OpenSSL, BoringSSL, Crypto++).

### 🔴 Mistake #4: Storing passwords with unsalted hashes

Two users with same password would have the same hash. Rainbow tables make cracking trivial. Always use a **unique salt** per password.

### 🔴 Mistake #5: Ignoring quantum threats

Public key algorithms (RSA, ECC) are broken by large‑scale quantum computers. Consider **post‑quantum cryptography** (Kyber, Dilithium) for long‑term secrets.

---

## ✅ Operational Checklists

### ✔️ Encrypting a file (symmetric)

- [ ] Choose an authenticated cipher (AES‑256‑GCM or ChaCha20‑Poly1305).
- [ ] Generate a random 256‑bit key (or derive from strong password with Argon2id).
- [ ] Generate a unique nonce/IV (12 bytes for GCM, 24 for XChaCha20).
- [ ] Encrypt and store nonce + ciphertext + tag together.
- [ ] Securely delete the plaintext (use `secure_zero` or `shred`).

### ✔️ Verifying file integrity

- [ ] Obtain the trusted hash (e.g., SHA‑512) of the original file.
- [ ] Compute hash of the downloaded file: `sha512sum file`.
- [ ] Compare byte‑by‑byte (not just a quick glance).
- [ ] Preferably use signed hashes (via GPG) to prevent MITM.

### ✔️ Generating a secure password hash

- [ ] Use **Argon2id** (libsodium, `argon2` CLI).
- [ ] Set parameters: time cost = 3, memory = 512MB, parallelism = 4.
- [ ] Generate a random salt (16–32 bytes).
- [ ] Run Argon2id on password + salt → output hash.
- [ ] Store salt, parameters, and hash in a single encoded string.
- [ ] Never store the password itself.

### ✔️ Encrypting for sharing with a recipient (hybrid)

- [ ] Obtain recipient’s public key (RSA, ECC, or Kyber).
- [ ] Generate a random symmetric session key (256 bits) and nonce.
- [ ] Encrypt the file symmetrically (AEAD).
- [ ] Encrypt the session key with the recipient’s public key.
- [ ] Package encrypted key + nonce + ciphertext + tag.
- [ ] Send to recipient.

---

## 📚 Further Reading

- **🔒 libsodium Documentation** – [https://doc.libsodium.org](https://doc.libsodium.org)
- **📖 “The Code Book” by Simon Singh** (historical overview)
- **🛡️ NIST Cryptographic Standards** – [https://csrc.nist.gov](https://csrc.nist.gov)
- **🔢 SHA‑3 Standard (FIPS 202)** – [https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.202.pdf](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.202.pdf)
- **🧅 Post‑Quantum Cryptography (NIST)** – [https://csrc.nist.gov/projects/post-quantum-cryptography](https://csrc.nist.gov/projects/post-quantum-cryptography)
- **🐍 Cryptography 101 (Practical Cryptography for Developers)** – [https://cryptobook.nakov.com](https://cryptobook.nakov.com)

---

*This document is part of the OpSec Documentation Hub. For corrections or additions, please open an issue on GitHub.*
```
