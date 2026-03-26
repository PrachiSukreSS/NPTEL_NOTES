# NPTEL Network Security: Complete Quick Revision Notes (Batch 2 - Modules 32-42)

---

## Module 32: IPsec and Virtual Private Networks (VPNs) - Part 1

### 32.1 Overview of IPsec
- Provides **network-layer security** between two nodes (hosts or routers)
- Security services provided:
  - **Confidentiality**: Payload (transport layer header + application data) is encrypted
  - **End-point Authentication**
  - **Message Integrity**
  - **Replay Detection**: Detects duplicate, deleted, or reordered packets

### 32.2 Virtual Private Networks (VPNs)
- **Purpose**: Securely connect multiple office locations and remote employees over public Internet
- **Key Idea**: From users' perspective, it behaves like a private network using leased lines, but actually uses public Internet

**Common VPN Design:**
- Each office has a **gateway router**
- **Tunnels** created through Internet between:
  - Gateway routers (office-to-office)
  - Gateway routers and remote employees

### 32.3 IPsec Operation

**Security Association (SA):**
- A **unidirectional** (simplex) logical connection
- If both parties need bidirectional communication → **two SAs** (one in each direction)

**Example:**
- Corporate VPN with: 1 headquarters, 1 branch office, n traveling salespersons
- Total SAs = 2n + 2

### 32.4 SA State Information
Each SA maintains in Security Association Database (SAD):

| Field | Description |
|-------|-------------|
| SPI | 32-bit Security Parameter Index (unique ID) |
| Source/Destination IP | Origin and destination interfaces |
| Encryption Algorithm | e.g., 3DES with CBC |
| Encryption Key | Key for encryption |
| MAC Algorithm | e.g., MD5 |
| Authentication Key | Key for MAC computation |

### 32.5 Internet Key Exchange (IKE) Protocol
- Used to establish SAs
- Similar to SSL handshake:
  - Exchange certificates (public keys) for authentication
  - Negotiate encryption and MAC algorithms
  - Exchange key material to derive keys

---

## Module 33: IPsec and VPNs - Part 2

### 33.1 Encapsulating Security Payload (ESP)
- IPsec protocol providing:
  - Confidentiality (encryption)
  - Message integrity (MAC)
  - Replay detection (sequence numbers)

### 33.2 IPsec Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| **Transport Mode** | ESP payload = higher layer message (UDP/TCP) | Host-to-host communication |
| **Tunnel Mode** | ESP payload = entire IP packet | VPNs (gateway-to-gateway) |

### 33.3 Tunnel Mode in VPN
- **Inner IP packet**: Original source/destination (e.g., host in headquarters)
- **Outer IP packet**: Gateway router source/destination
- When received, payload IP packet is extracted and forwarded

### 33.4 VPN Advantages
- **More flexible** than leased lines (tunnels can be set up on demand)
- **Traffic analysis defense**: All traffic between gateway routers aggregated into two SAs → intruders cannot see traffic patterns between individual machines

---

## Module 34: IPsec and VPNs - Part 3 (IKE Protocol Details)

### 34.1 IKE Two-Phase Structure

| Phase | Purpose | Cryptographic Operations |
|-------|---------|--------------------------|
| **Phase 1** | Establish bi-directional IKE SA (secure channel) | Diffie-Hellman (expensive) |
| **Phase 2** | Establish IPsec SAs (one per direction) | Faster (no public key crypto) |

**Why two phases?**
- Good practice to periodically change keys
- Phase 2 is much faster → can be executed frequently
- Phase 1 establishes long-term keys; Phase 2 generates short-term session keys

### 34.2 Clogging Attack
- **Attack**: Intruder forges source address and sends public DH keys to victim
- **Effect**: Victim performs expensive modular exponentiation repeatedly → system clogged
- **Defense**: **Cookies** used in IKE protocol

### 34.3 IKE Phase 1 (Shared Secret Case)

| Step | Action |
|------|--------|
| 1 | A sends: proposed algorithms SA_A, cookie C_A |
| 2 | B sends: selected algorithms SA_B, cookie C_B |
| 3 | A sends: nonce R_A, public DH key g^a mod p |
| 4 | B sends: nonce R_B, public DH key g^b mod p |
| 5 | A sends: encrypted identity "A", MAC_A |
| 6 | B sends: encrypted identity "B", MAC_B |

**Key Derivation:**
- Encryption and MAC keys = function(C_A, C_B, R_A, R_B, s, g^ab mod p)

**Security Features:**
- **Man-in-the-middle protection**: MACs include secret s (unknown to intruder)
- **Nonces**: Protect against connection replay attacks
- **MAC includes SA_A, SA_B**: Detects algorithm modification
- **Forward secrecy**: Provided by DH key exchange

### 34.4 Identity Protection Issue
- **Problem**: Identity "A" is encrypted, but B needs to know which key to use for decryption
- **Solution**: B attempts decryption with keys of all entities associated with source IP address

---

## Module 35: Securing Wireless LANs - Part 1 (WEP)

### 35.1 Why Wi-Fi Security is Important
- Radio waves propagate beyond building boundaries
- Intruders can:
  - Intercept transmissions with packet sniffer
  - Transmit packets masquerading as AP or legitimate device

### 35.2 WEP (Wired Equivalent Privacy)
- Original security mechanism in 802.11 (late 1990s)
- **Later found to have severe flaws** → deprecated

**WEP Authentication Protocol (ap4.0):**
1. Host requests authentication
2. AP sends 128-byte nonce (plaintext)
3. Host encrypts nonce with shared key, sends to AP
4. AP decrypts and verifies

### 35.3 WEP Encryption
- 40-bit shared symmetric key K_S
- 24-bit IV appended to K_S → 64-bit key for each frame
- RC4 stream cipher generates keystream
- **Encryption**: plaintext XOR keystream

**Why IV changes per frame:**
1. Prevents identical plaintext → identical ciphertext
2. Avoids using same RC4 initialization state repeatedly

---

## Module 36: Securing Wireless LANs - Part 2 (WEP Weaknesses)

### 36.1 Major WEP Weaknesses

| Weakness | Description |
|----------|-------------|
| **No mutual authentication** | Mobile device cannot verify AP legitimacy |
| **Same key for auth and encryption** | Poor security practice |
| **No session keys** | Long-term key used for all sessions |
| **IV too small (24 bits)** | Keystream repeats frequently in busy networks |
| **Replay protection absent** | Sequence number not encrypted or MAC-protected |

### 36.2 Authentication Bypass Attack
1. Sniff nonce P (plaintext) and ciphertext C = P ⊕ keystream
2. Recover keystream = P ⊕ C
3. Later, request authentication, get nonce P'
4. Send C' = P' ⊕ keystream with same IV → authenticated without knowing key

### 36.3 Message Integrity Failure
**WEP Method:**
- 4-byte CRC computed on plaintext
- CRC XORed with keystream

**Why it fails:**
- CRC is **linear**: CRC(m₁ ⊕ m₂) = CRC(m₁) ⊕ CRC(m₂)
- XOR property: if x ⊕ k = y, then x̄ ⊕ k = ȳ
- Attacker can change bits in ciphertext and correspondingly change CRC bits → valid message with modified content

### 36.4 Key Recovery Attack
- Automated tools available (e.g., Fluhrer-Mantin-Shamir attack)
- Can recover key in **as little as one minute** depending on traffic

---

## Module 37: Securing Wireless LANs - Part 3 (WPA and 802.11i)

### 37.1 Evolution of Wi-Fi Security

| Standard | Year | Description |
|----------|------|-------------|
| WEP | 1999 | Original (insecure) |
| WPA | 2003 | Interim solution (TKIP) |
| 802.11i (WPA2) | 2004 | Final secure standard |
| 802.11w | 2009 | Management frame protection |
| WPA3 | 2018 | Latest standard |

### 37.2 802.11i (WPA2) Improvements
- **Stronger encryption** than WEP
- **Mutual authentication** (device and AP authenticate each other)
- **Key distribution mechanism**
- **Stronger message integrity**

### 37.3 Authentication Server Model
- **Three entities**: Station (client), AP, Authentication Server (AS)
- AP acts as **relay** between station and AS
- **Advantages**:
  - AP complexity/cost low
  - Sensitive authentication data confined to AS (not replicated at each AP)

### 37.4 802.11i Four Phases

| Phase | Description |
|-------|-------------|
| **1. Discovery** | AP sends beacons; station requests specific auth/encryption |
| **2 & 3. Mutual Authentication** | EAP protocol; result = Pairwise Master Key (PMK) |
| **4. Key Generation** | Derive Pairwise Transient Key (PTK) from PMK + nonces |

### 37.5 EAP Authentication Methods

| Method | Description | Security |
|--------|-------------|----------|
| **EAP-MD5** | Station sends hash of password | Insecure (one-way, replayable) |
| **EAP-TLS** | Based on TLS, mutual auth, certificates required for both | Most secure |
| **EAP-TTLS** | Only server certificate required; password sent over secure tunnel | Practical compromise |

### 37.6 PSK Mode (Pre-Shared Key)
- Used in **homes and small offices**
- Shared keys (passwords) manually installed in AP
- PMK derived from PSK
- Same key derivation process as authentication server mode

---

## Module 38: Securing Wireless LANs - Part 4 (Key Derivation)

### 38.1 Key Hierarchy

```
PSK (Pre-Shared Key) or PMK (from AS)
         ↓
   256-bit PMK
         ↓
   384-bit PTK (Pairwise Transient Key)
         ↓
Three 128-bit chunks:
├── TK (Temporal Key) → encryption + MIC
├── KCK (Key Confirmation Key) → integrity of handshake messages
└── KEK (Key Encryption Key) → encrypt group key
```

### 38.2 PTK Derivation
```
PTK = prf(PMK, N_A, N_S, MAC_A, MAC_S)
```
Where:
- **N_A, N_S**: Nonces from AP and station
- **MAC_A, MAC_S**: MAC addresses
- Nonces defend against replay attacks

### 38.3 Four-Way Handshake

| Message | Direction | Content | Purpose |
|---------|-----------|---------|---------|
| 1 | AP → Station | N_A | Start handshake |
| 2 | Station → AP | N_S, cipher suite, MIC (using KCK) | Station computes PTK |
| 3 | AP → Station | GTK (encrypted with KEK), MIC | Distribute group key |
| 4 | Station → AP | ACK | Confirm completion |

### 38.4 Why PMK Not Used Directly?
- Long-term keys should not be used for encryption
- **Temporal Key** is a session key, different for each session
- Even if PMK is same for all sessions (e.g., PSK mode), TK varies due to nonces

### 38.5 RADIUS and Diameter
- **RADIUS**: Popular authentication server standard (IETF)
- **Diameter**: Improved version of RADIUS
- Used in Wi-Fi and other contexts (dial-up, cloud computing)

---

## Module 39: Securing Wireless LANs - Part 5 (TKIP)

### 39.1 TKIP (Temporal Key Integrity Protocol)
- **Used in WPA** (interim solution)
- Designed to run on existing WEP hardware
- Retains RC4 but adds security improvements

### 39.2 WEP vs TKIP

| Feature | WEP | TKIP |
|---------|-----|------|
| Key size | 40-bit + 24-bit IV = 64-bit | 128-bit |
| Key generation | Simple IV concatenation | Two-phase key mixing |
| Keystream collisions | Frequent | Very rare |
| Message integrity | Linear CRC (broken) | MIC/Michael (non-linear) |

### 39.3 TKIP Two-Phase Key Mixing

**Inputs:**
- 128-bit Temporal Key (TK)
- Sender's MAC address
- 48-bit frame sequence counter (increments per frame)

**Process:**
- **PRF2** (fast): Uses least significant 16 bits of sequence counter → changes every frame
- **PRF1** (slower): Uses most significant 32 bits → changes every 65,536 frames

**Benefits:**
- Large key space (128 bits)
- Keystream collisions negligible
- Eliminates WEP key recovery attacks

### 39.4 TKIP Message Integrity (MIC/Michael)
- **64-bit MIC**
- **Non-linear**: MIC(m₁ ⊕ m₂) ≠ MIC(m₁) ⊕ MIC(m₂)
- Uses simple operations (no multiplication) for hardware compatibility
- Much stronger than CRC, but not as secure as keyed cryptographic hash

### 39.5 TKIP Deprecation
- TKIP **no longer considered secure** (new attacks discovered)
- Deprecated in 2012 revision of 802.11 standard

---

## Module 40: Securing Wireless LANs - Part 6 (CCMP)

### 40.1 CCMP (Counter Mode with CBC-MAC Protocol)
- **Used in WPA2** (final solution)
- Uses **AES** (block cipher) instead of RC4
- Provides both encryption and message integrity

### 40.2 Key Differences from TKIP

| Feature | TKIP | CCMP |
|---------|------|------|
| Cipher | RC4 (stream) | AES (block) |
| Key per frame | Fresh key each frame | Same key each frame |
| Encryption mode | RC4 keystream | AES Counter Mode |
| MIC method | Michael (non-linear) | CBC-MAC |
| Security | Deprecated | Strong (AES-based) |

### 40.3 CCMP Components
- **Packet Number (PN)**: 48-bit counter, increments per packet
- **Temporal Key (TK)**: 128-bit key for both encryption and MIC
- **IV for MIC**: Includes PN
- **IV for encryption**: Different construction (ensures security despite same key)

### 40.4 CCMP Message Integrity (CBC-MAC)
- AES in **CBC mode** with block size 128 bits
- Process: P₁ → AES → ⊕ P₂ → AES → ... → final block
- Output: 128 bits → lower 64 bits discarded → 64-bit MIC
- MIC covers:
  - Frame data
  - Certain MAC header fields (source/destination addresses)

**Security**: CBC-MAC is standardized and well-analyzed

### 40.5 CCMP Encryption (Counter Mode)
- Frame data + MIC encrypted using AES in counter mode
- **Counter**: Function of PN and IV
- Receiver performs decryption then MIC verification

### 40.6 Same Key for Encryption and MIC?
- Generally **not good practice** to use same key for two functions
- CCMP uses **different IV constructions** for encryption vs MIC
- Proven secure by cryptographers (Jonsson, 2002)

### 40.7 Replay Attack Prevention
- Receiver tracks PN of last frame received
- If PN ≤ last received → discard frame
- If PN > last received but MIC fails → discard (IV mismatch)
- **Result**: Replayed frames are discarded

---

## Module 41: Securing Wireless LANs - Part 7 (Management Frame Security)

### 41.1 Problem: Unprotected Management Frames
- 802.11i protects **data frames only**
- Management frames sent without protection:
  - Beacon, Probe request/response
  - Authentication request/response
  - Association request/response
  - Disassociation, Deauthentication

### 41.2 Why Management Frame Security Needed
- New features (802.11r, 802.11k, 802.11v) exchange sensitive info in management frames
- DoS attacks possible due to unprotected management frames

### 41.3 Types of Management Frames

| Frame | Purpose |
|-------|---------|
| **Beacon** | AP broadcasts characteristics (rates, protocols, etc.) |
| **Probe Request/Response** | Active scanning to discover networks |
| **Authentication Request/Response** | Initial 802.11 device verification (not security) |
| **Association Request/Response** | Establish AP responsibility for forwarding |
| **Disassociation** | Terminate association (roaming, invalid params) |
| **Deauthentication** | Terminate all communications |

### 41.4 DoS Attacks on Unprotected Management Frames

| Attack | Method | Impact |
|--------|--------|--------|
| **Deauthentication** | Spoof deauth frame | Client exits authenticated state; must re-authenticate |
| **Disassociation** | Spoof disassoc frame | Client exits associated state |
| **Power saving** | Spoof polling/TIM | AP discards buffered packets; client misses wake-up |

### 41.5 IEEE 802.11w Amendment (2009)
- Provides **protection for management frames**
- Uses existing 802.11i mechanisms (CCMP)
- **Protected frames** (after key establishment):
  - Disassociate
  - Deauthenticate
- **Unprotected frames** (before key establishment):
  - Beacon, Probe request/response
  - Authentication, Association frames

### 41.6 WPA3 (2018)
- Makes management frame protection **mandatory**
- Uses SAE (Simultaneous Authentication of Equals)/Dragonfly handshake

### 41.7 Remaining Vulnerabilities in 802.11w

| Vulnerability | Description |
|---------------|-------------|
| **Pre-handshake deauth** | Attacker sends deauth before four-way handshake completes |
| **Beacon flood** | Flood with fake beacons (beacons unprotected) |
| **Probe flood** | Flood with probe requests/responses |

**Defense**: Intrusion Detection Systems (IDS)

---

## Module 42: Securing Wireless LANs - Part 8 (Summary of Wi-Fi Security Evolution)

### 42.1 Complete Wi-Fi Security Evolution Timeline

```
1999 ─── WEP (insecure, deprecated)
  │
2003 ─── WPA (TKIP, interim solution, now deprecated)
  │
2004 ─── 802.11i / WPA2 (CCMP, AES-based, secure)
  │
2009 ─── 802.11w (management frame protection)
  │
2018 ─── WPA3 (mandatory management frame protection + SAE)
```

### 42.2 Summary of Security Mechanisms

| Feature | WEP | WPA (TKIP) | WPA2 (CCMP) | WPA3 |
|---------|-----|------------|-------------|------|
| **Cipher** | RC4 | RC4 | AES | AES |
| **Key size** | 64-bit | 128-bit | 128-bit | 128/256-bit |
| **Encryption mode** | Stream | Stream (with mixing) | Counter Mode | Counter Mode |
| **MIC** | CRC (linear) | Michael (non-linear) | CBC-MAC | CBC-MAC |
| **Authentication** | One-way | Mutual (EAP/PSK) | Mutual (EAP/PSK) | SAE (stronger) |
| **Management frames** | Unprotected | Unprotected | Unprotected | Protected (802.11w) |
| **Key management** | None | Four-way handshake | Four-way handshake | Enhanced |
| **Status** | Deprecated | Deprecated | Current | Latest |

---

## Quick Reference Table: Wi-Fi Security Standards

| Standard | Also Known As | Encryption | MIC | Authentication | Status |
|----------|---------------|------------|-----|----------------|--------|
| 802.11 (1999) | WEP | RC4 (64-bit) | CRC | One-way | Deprecated |
| 802.11i draft (2003) | WPA | RC4 (TKIP) | Michael | EAP/PSK | Deprecated |
| 802.11i (2004) | WPA2 | AES-CCMP | CBC-MAC | EAP/PSK | Current |
| 802.11w (2009) | Management Frame Protection | AES-CCMP | CBC-MAC | Same as WPA2 | Part of WPA2/3 |
| 802.11ac/ax | WPA3 | AES-CCMP/GCM | GCM | SAE | Latest |

---

## Key Formulas and Concepts

### Key Derivation Formulas

| Key | Formula |
|-----|---------|
| PTK (802.11i) | prf(PMK, N_A, N_S, MAC_A, MAC_S) |
| TKIP encryption key | Two-phase mix(TK, MAC, sequence counter) |
| CCMP MIC | CBC-MAC(TK, data, IV) |
| CCMP encryption | Counter Mode(TK, data, IV) |

### WEP Attack Formula
```
Keystream = Plaintext ⊕ Ciphertext
```

### Security Properties Summary

| Attack | WEP | TKIP | CCMP |
|--------|-----|------|------|
| Keystream recovery | Easy (IV reuse) | Very hard | N/A (block cipher) |
| Message forgery | Easy (linear CRC) | Harder (non-linear MIC) | Very hard (CBC-MAC) |
| Key recovery | Minutes | Infeasible | Infeasible |
| Replay | Vulnerable | Protected | Protected |

---

**MCQ Tip for Wi-Fi Security:**
- Focus on **why WEP failed** (IV too small, linear CRC, no mutual auth, same key for everything)
- Know the **difference** between TKIP (RC4-based, WPA) and CCMP (AES-based, WPA2)
- Understand **why management frame protection** (802.11w) was needed
- Remember **four-way handshake** and **key hierarchy** (PMK → PTK → TK, KCK, KEK)
