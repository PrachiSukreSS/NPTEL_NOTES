# NPTEL Network Security: Complete Quick Revision Notes (Batch 2 - Continued)

---

## Module 12: Advanced Authentication Protocols

### 12.1 Weakness of ap4.0 (One-Way Authentication)
- **Problem**: Bob authenticates Alice, but Alice does NOT authenticate Bob
- **Consequence**: Intruder can intercept "I am Alice" message, send any nonce R to Alice, ignore her response → Alice thinks she's talking to Bob

### 12.2 Mutual Authentication (Using Symmetric Key)

**Three-Message Protocol:**
1. Alice → Bob: "I am Alice"
2. Bob → Alice: R₁
3. Alice → Bob: f(K_{A-B}, R₁)
4. Bob → Alice: R₂
5. Alice → Bob: f(K_{A-B}, R₂)

- **f(K, R)** can be:
  - K(R) [encryption] OR
  - H(R, K) [hash - faster, no encryption]

### 12.3 Reflection Attack

**What it is:** Attack where intruder uses multiple sessions to reflect challenges back

**How it works:**
1. Trudy opens session with Bob, receives R₁, f(K, R₂)
2. Trudy opens second session, sends "I'm Alice, R₁"
3. Bob sends f(K, R₁) in second session
4. Trudy uses this to complete first session

**Defenses:**
- Use different keys for authentication in each direction
- Include direction identifier in f(K, R, direction)
- Use nonces with different structures

---

## Module 13: Lamport's Hash (One-Time Password Scheme)

### 13.1 Overview
- Provides one-way authentication **without encryption**
- Defends against **both** eavesdropping AND server database reading attack

### 13.2 Setup Phase
1. Alice chooses password
2. Workstation computes hashⁿ(password) (n is large, e.g., 1000)
3. Sends `<Alice, n, hashⁿ(password)>` to Bob securely (once per n authentications)

### 13.3 Authentication Phase

| Step | Action |
|------|--------|
| 1 | Alice sends username "Alice" |
| 2 | Bob sends current n to Alice |
| 3 | Alice computes x = hashⁿ⁻¹(password), sends to Bob |
| 4 | Bob computes hash(x) and compares with stored hashⁿ(password) |
| 5 | If match, success; Bob updates to `<n-1, x>` |

### 13.4 Security Properties

| Attack Type | Defense |
|-------------|---------|
| **Eavesdropping** | Trudy gets hashⁿ⁻¹(password) but cannot compute hashⁿ⁻²(password) for next authentication |
| **Server database reading** | Trudy gets hashⁿ(password) but cannot compute hashⁿ⁻¹(password) |

### 13.5 Vulnerabilities & Defenses

| Attack | Defense |
|--------|---------|
| **Same password for multiple servers** | Use hashⁿ(password \| servername) |
| **Small n attack** (impersonator sends small n) | Maintain counter at Alice to track correct n value |

---

## Module 14: Session Key Establishment

### 14.1 Why Separate Session Keys?
1. More encrypted data → more information for attacker to find key
2. Prevents replay of old messages into current conversation
3. Limits damage if key is compromised

### 14.2 Session Key from Shared Symmetric Key

**Insecure Approaches:**
- Using K_{A-B}(R) as session key → Trudy eavesdropping knows it
- Using K_{A-B}(R+1) → vulnerable to replay attack

**Secure Approach:**
- Use g(K_{A-B})(R) where g(K) is modified version of K
- Example: (K_{A-B}+1)(R) is secure

### 14.3 Session Key from Public Key Authentication

**Insecure:**
- Alice sends {R}Bob (encrypted with Bob's public key) → Trudy can replace with {T}Bob

**Secure:**
- Alice sends {R}Bob signed with her private key → { {R}Bob }Alice
- Trudy cannot forge Alice's signature

### 14.4 Best Practice
- Establish **four session keys**:
  - E_A: encryption from Alice to Bob
  - E_B: encryption from Bob to Alice
  - M_A: MAC from Alice to Bob
  - M_B: MAC from Bob to Alice

---

## Module 15: Authentication Using Key Distribution Center (KDC)

### 15.1 KDC Concept
- Trusted central node that distributes session keys
- Each network node shares a secret key with KDC

### 15.2 Basic KDC Protocol

```
1. Alice → KDC: "I want to talk to Bob"
2. KDC → Alice: K_{A-KDC}(K_{AB}), K_{B-KDC}(K_{AB}, "Alice")
3. Alice → Bob: K_{B-KDC}(K_{AB}, "Alice")
```

### 15.3 Advantages & Disadvantages

| Advantages | Disadvantages |
|------------|---------------|
| Single point of configuration | Single point of failure |
| Easy key management | Performance bottleneck |
| | If KDC compromised, all resources vulnerable |

### 15.4 Mitigation
- Use multiple KDCs with shared database
- Replication adds complexity and cost

---

## Module 16: Needham-Schroeder Protocol

### 16.1 Protocol Messages

```
1. Alice → KDC: Alice, Bob, N₁
2. KDC → Alice: K_{A-KDC}(K_{AB}, Bob, N₁, K_{B-KDC}(K_{AB}, Alice))
3. Alice → Bob: K_{B-KDC}(K_{AB}, Alice), K_{AB}(N₂)
4. Bob → Alice: K_{AB}(N₂-1), K_{AB}(N₃)
5. Alice → Bob: K_{AB}(N₃-1)
```

### 16.2 Purpose of Nonces
- **N₁**: Prevents replay of old KDC messages after key compromise
- **N₂, N₃**: Mutual authentication

### 16.3 Vulnerabilities & Fixes

| Vulnerability | Fix |
|---------------|-----|
| Trudy replays old ticket after Alice changes key | Expanded Needham-Schroeder: Bob sends nonce N_B to KDC |
| Reflection attack on message 4 | Encrypt so K_{AB}(N₂-1) cannot be deduced from K_{AB}(N₂-1, N₃) |

---

## Module 17: Public Key Infrastructure (PKI)

### 17.1 Problem: How to Trust a Public Key?

**Attack Scenario:**
1. Alice requests Bob's public key from his website
2. Trudy intercepts, sends fake webpage with her own public key
3. Alice encrypts message with Trudy's key → Trudy reads it

### 17.2 Certification Authority (CA) Solution

**Certificate Contents (X.509 format):**
| Field | Description |
|-------|-------------|
| Version | X.509 version |
| Serial Number | Unique ID from CA |
| Signature Algorithm | Algorithm CA used to sign |
| Issuer Name | CA identity |
| Validity Period | Start and end dates |
| Subject Name | Owner's identity |
| Subject Public Key | Owner's public key |
| Signature | CA's signature on hash of certificate |

### 17.3 How CA Prevents Attack
- Bob gets certificate from CA containing his public key and identity
- Certificate is signed by CA
- Alice verifies CA's signature using CA's public key (preloaded in browser)
- Trudy cannot forge CA's signature

### 17.4 PKI Models

| Model | Description | Pros | Cons |
|-------|-------------|------|------|
| **Monopoly** | Single universal CA | Simple | Single point of failure, monopoly pricing |
| **Monopoly + RA** | CA delegates identity verification to RAs | More convenient | Same disadvantages as monopoly |
| **Hierarchy** | Root CA certifies subordinate CAs | Scalable | Chain of trust must be verified |
| **Oligarchy** | Multiple root CAs preloaded in browsers | Competition, no single point of failure | 80+ roots, user cannot verify all |
| **Anarchy (Web of Trust)** | Users certify each other | No central authority | Scaling problems, trust ambiguity |

### 17.5 Certificate Revocation

**Certificate Revocation List (CRL):**
- List of revoked certificates issued by CA
- Verifiers must check CRL before trusting certificate

**Delta CRL:**
- Lists only changes since last full CRL
- More efficient for frequent updates
- Full CRL posted periodically; delta CRL posted more often

---

## Module 18: Secure Email - PGP

### 18.1 PGP Features
| Feature | Implementation |
|---------|----------------|
| Confidentiality | IDEA (128-bit symmetric key) |
| Message Integrity | MD5 hash with digital signature |
| Compression | Lempel-Ziv algorithm |
| Key Management | Web of Trust (not CAs) |

### 18.2 PGP Operation

**Sender (Alice) Actions:**
1. Hash message with MD5 → sign with D_A (RSA) → append to message
2. Compress signed message (ZIP)
3. Generate random 128-bit key K_M
4. Encrypt compressed message with IDEA using K_M
5. Encrypt K_M with RSA using Bob's public key E_B
6. Convert to base64 format (safe for email transmission)

**Receiver (Bob) Actions:**
1. Reverse base64 encoding
2. Decrypt K_M using D_B (RSA)
3. Decrypt message using K_M (IDEA)
4. Decompress
5. Verify signature using E_A

### 18.3 Efficiency
- **RSA used only for:**
  - Signing 128-bit MD5 hash
  - Encrypting 128-bit IDEA key
- **IDEA used for bulk data (much faster than RSA)**

### 18.4 Web of Trust vs CA
| PGP Web of Trust | S/MIME with CA |
|------------------|----------------|
| Users certify each other | Central authorities certify |
| Confidence levels assigned | Binary trust |
| Multiple certificates increase trust | Single certificate sufficient |
| Decentralized | Hierarchical |

---

## Module 19: Secure Email - S/MIME

### 19.1 Overview
- Security enhancement to MIME email format
- Uses **X.509 certificates** from CAs (unlike PGP)
- Sender's certificate **included in email**

### 19.2 S/MIME Operation

```
1. Sender creates message
2. SHA-256 generates 256-bit hash
3. Hash signed with sender's private key (RSA)
4. Signature appended to message
5. Sender's certificate appended
6. Message encrypted with one-time AES key (CBC mode)
7. One-time key encrypted with receiver's public key (RSA)
```

### 19.3 PGP vs S/MIME

| Feature | PGP | S/MIME |
|---------|-----|--------|
| Key Management | Web of Trust | X.509 CAs |
| Certificate in email? | No (must obtain separately) | Yes |
| Default Hash | MD5 | SHA-256 |
| Default Symmetric Cipher | IDEA | AES |

---

## Module 20: SSL/TLS

### 20.1 Overview
- Adds security to TCP: confidentiality, integrity, server authentication, client authentication
- TLS is standardized version of SSL
- Used for HTTPS (URL begins with https://)

### 20.2 SSL Three Phases
1. **Handshake** - Establish connection, authenticate server, agree on crypto algorithms
2. **Key Derivation** - Generate session keys from Master Secret
3. **Data Transfer** - Encrypted, authenticated data exchange

### 20.3 Simplified SSL Handshake

```
1. TCP connection established
2. Server sends certificate (with public key K_A⁺)
3. Client generates Master Secret (MS), encrypts with K_A⁺, sends EMS
4. Server decrypts EMS with private key
```

### 20.4 Key Derivation
From MS, generate **four symmetric keys**:

| Key | Purpose |
|-----|---------|
| E_B | Encryption: Bob → Alice |
| E_A | Encryption: Alice → Bob |
| M_B | MAC: Bob → Alice |
| M_A | MAC: Alice → Bob |

**Why four separate keys?**
- Same key for encryption and MAC could leak information
- Same key in both directions allows reflection attacks

### 20.5 SSL Record Format

| Type | Version | Length | Data | MAC |
|------|---------|--------|------|-----|
| (1 byte) | (2 bytes) | (2 bytes) | (variable) | (variable) |

- Type, version, length are **not encrypted** but **included in MAC** (so tampering detectable)

### 20.6 SSL Sequence Numbers
- Not included in record (saves bandwidth)
- Maintained in counters at each end
- Included in MAC calculation
- Protects against replay, reordering, deletion

---

## Module 21: Actual SSL Handshake

### 21.1 Full Handshake Steps

| Step | Action |
|------|--------|
| 1 | Client sends: list of crypto algorithms, client nonce |
| 2 | Server selects algorithms, sends: choices, server certificate, server nonce |
| 3 | Client verifies certificate, generates Pre-Master Secret (PMS), encrypts with server's public key, sends to server |
| 4 | Both compute Master Secret (MS) from PMS and nonces; generate session keys |
| 5 | Client sends MAC of all handshake messages |
| 6 | Server sends MAC of all handshake messages |

### 21.2 Purpose of Nonces
- Client nonce → protects client from connection replay attack
- Server nonce → protects server from connection replay attack

### 21.3 Purpose of Handshake MACs
- Detects modification by intruder
- Example: Trudy may delete strong algorithms from list, forcing weak algorithm selection

### 21.4 Server Authentication
- Yes: server certificate + client verifies + MAC in step 6

### 21.5 Client Authentication (Optional)

**If performed:**
1. Server sends "Certificate Request" after its certificate
2. Client sends its certificate
3. Client sends "Certificate Verify" message: signed hash of all handshake messages
4. Server verifies using client's public key

**Why replay doesn't work:** Hash includes server and client nonces

---

## Module 22: SSL Key Exchange Methods

### 22.1 RSA Method
- Client generates random PMS
- Encrypts with server's RSA public key
- Sends to server

### 22.2 Ephemeral Diffie-Hellman (DH)

**Process:**
1. Client and server generate one-time DH key pairs
2. DH public keys signed with RSA private key (prevents man-in-the-middle)
3. Shared secret computed using DH → becomes PMS

### 22.3 Forward Secrecy

| Method | Forward Secrecy? |
|--------|------------------|
| RSA key exchange | NO (if server's private key later compromised, all recorded sessions can be decrypted) |
| Ephemeral DH | YES (one-time keys, cannot decrypt recorded sessions even if long-term keys compromised) |

---

## Module 23: SSL Connection Closure

### 23.1 Problem with TCP FIN
- Intruder Trudy can send TCP FIN to close connection prematurely

### 23.2 SSL Solution
- Send SSL record with type field indicating connection close
- Type field included in MAC → tampering detectable

---

## Module 24: Summary - Security at Different Layers

| Layer | Protocol/System | Security Provided |
|-------|-----------------|-------------------|
| **Application** | PGP, S/MIME | Email security |
| **Transport** | SSL/TLS | Secures TCP connections |
| **Network** | IPSec, VPNs | Network-layer security |
| **Link** | 802.11i, 802.11w | Wi-Fi security |

### 24.1 Why Multiple Layers?
- **Link layer**: defends against sniffing on wireless channel
- **Transport layer**: server authentication, confidentiality from ISPs
- **Network layer**: hides traffic patterns between VPN endpoints

---

## Quick Reference Formulas & Tables

### Key Formulas

| Concept | Formula |
|---------|---------|
| Lamport's Hash | hashⁿ(password) |
| Session key (symmetric) | g(K_{A-B})(R) |
| PGP encryption | IDEA(K_M, compress(sign(P))) |
| SSL record MAC | H(data, M_key, seq_num) |

### Recommended Key Sizes

| Item | Recommendation |
|------|----------------|
| RSA modulus n | ≥ 2048 bits |
| Symmetric key (IDEA, AES) | 128-256 bits |
| Hash output (collision resistance) | ≥ 256 bits (SHA-256) |
| DH prime p | ≥ 2048 bits |

### Security Properties Summary

| Property | Achieved By |
|----------|-------------|
| Confidentiality | Encryption (symmetric/public key) |
| Message Integrity | MAC, Digital Signature |
| Authentication | Nonce-based protocols, Certificates |
| Non-repudiation | Digital Signature |

---

**MCQ Tip:** Focus on understanding:
- The **evolution** of authentication protocols (ap1.0 → ap4.0)
- **Why** Lamport's Hash defends against both eavesdropping and server database reading
- **Why** four separate session keys are needed in SSL
- **Forward secrecy** concept and which key exchange method provides it
- **Difference** between PGP (Web of Trust) and S/MIME (X.509 certificates)
