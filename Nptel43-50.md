# NPTEL Network Security: Complete Quick Revision Notes (Modules 43-50)

---

## Module 43: Wireless Cellular Network Security - Part 1 (2G/3G Overview)

### 43.1 Cellular Network Basics
- **Cells**: Small areas (radius < 5 km) each served by a Base Station (BS)
- **Frequency Reuse**: Same frequencies used in far-apart cells → increases capacity
- **Spectrum**: Licensed from regulator → costly but exclusive access

### 43.2 2G and 3G Overview

| Generation | Technology | Key Features |
|------------|------------|--------------|
| **1G** | Analog | No security |
| **2G** | GSM | Better voice quality, international roaming, basic security |
| **3G** | UMTS | Mobile Internet, multimedia, videoconferencing, improved security |

### 43.3 Network Architecture Components

| Component | Full Form | Function |
|-----------|-----------|----------|
| **BTS** | Base Transceiver Station | Cell tower, wireless connection to phones |
| **BSC** | Base Station Controller | Controls multiple BTSs |
| **MSC** | Mobile Switching Centre | Call routing, billing |
| **HLR** | Home Location Register | Subscriber database (static + dynamic location info) |
| **VLR** | Visitor Location Register | Database of roaming users in current network |
| **AuC** | Authentication Centre | Stores permanent security keys |

### 43.4 SIM Card (Subscriber Identity Module)
- Removable smart card
- Stores:
  - **IMSI**: 15-digit International Mobile Subscriber Identity (permanent)
  - **Kᵢ**: 128-bit subscriber authentication key (known only to SIM and HLR)
  - **PIN**: Optional lock (rarely used)

### 43.5 Security Goals
| Goal | GSM/UMTS Implementation |
|------|------------------------|
| **User Identity Confidentiality** | Use TMSI (Temporary) instead of IMSI |
| **Message Confidentiality** | Encryption of user data and signalling |
| **Entity Authentication** | MSC authenticates subscriber |
| **Message Integrity** | Signalling messages integrity protected |

---

## Module 44: Wireless Cellular Network Security - Part 2 (GSM Security)

### 44.1 GSM Authentication and Key Agreement

**Step 1: Authorization Request**
- Cellphone sends IMSI/TMSI + supported encryption algorithms to MSC

**Step 2: Challenge Generation (Home Network)**
- HLR/AuC generates:
  - **RAND**: Random challenge
  - **XRES**: Expected response = A3(RAND, Kᵢ)
  - **Kc**: Encryption key = A8(RAND, Kᵢ)
- Sends authentication triplet <RAND, XRES, Kc> to MSC

**Step 3: Cellphone Response**
- SIM computes **SRES = A3(RAND, Kᵢ)**
- Cellphone sends SRES to MSC
- MSC compares SRES with XRES → if match, subscriber is genuine

**Step 4: Encryption Key**
- SIM computes **Kc = A8(RAND, Kᵢ)**
- MSC sends Kc to base station

### 44.2 GSM Encryption (A5 Stream Cipher)
```
KEYSTREAM = A5(Kc, FRAME NO.)
Ciphertext = Plaintext ⊕ Keystream
```
- **Frame number**: 22-bit, increments per frame → keystream changes frequently
- **Kc**: 64-bit (often truncated to 54 bits + 10 zeros)

### 44.3 Key Functions

| Function | Location | Purpose |
|----------|----------|---------|
| **A3** | SIM | Compute SRES |
| **A8** | SIM | Compute Kc |
| **A5** | Phone | Keystream generation (not on SIM) |

### 44.4 Major GSM Security Weaknesses

| Weakness | Description |
|----------|-------------|
| **No mutual authentication** | Network authenticates SIM, but SIM cannot authenticate network → **false base station attack** possible |
| **A5 vulnerabilities** | A5/1 and A5/2 can be cracked in seconds/minutes |
| **Weak Kc** | 64-bit key often truncated to 54 bits |
| **No integrity protection** | Signalling messages not integrity protected |
| **Limited encryption scope** | Only BTS-air interface encrypted; microwave links often unencrypted |
| **Cipher mode spoofing** | Attacker can send message instructing phone to suppress encryption |

---

## Module 45: Wireless Cellular Network Security - Part 3 (UMTS/3G Security)

### 45.1 UMTS Security Enhancements over GSM

| Feature | GSM | UMTS |
|---------|-----|------|
| **Mutual authentication** | No | Yes |
| **Signalling integrity** | No | Yes (individually protected) |
| **False base station attack** | Possible | Prevented |
| **Encryption algorithm** | A5 (COMP-128) | KASUMI (128-bit block cipher) |
| **Key length** | 64-bit | 128-bit |
| **Network domain security** | No | Yes (IPsec between network nodes) |

### 45.2 UMTS Authentication and Key Agreement

**Step 1: Authorization Request** (same as GSM)

**Step 2: Authentication Vector Generation (HLR/AuC)**

HLR generates:
- **RAND**: Random number
- **XRES** = F2(RAND, Kᵢ)
- **CK** = F3(RAND, Kᵢ) (Cipher Key)
- **IK** = F4(RAND, Kᵢ) (Integrity Key)
- **AK** = F5(RAND, Kᵢ) (Anonymity Key)

HLR computes MAC using F1:
```
MAC = F1(RAND, Kᵢ, AMF, SQN)
```
where:
- **AMF**: Authentication Management Field (key lifetime)
- **SQN**: Sequence number (known only to HLR and SIM)

**Authentication Token (AUTN)**:
```
AUTN = <SQN ⊕ AK, AMF, MAC>
```

**Authentication Vector (Quintuplet)**:
```
AV = <RAND, XRES, CK, IK, AUTN>
```
- Up to 5 AVs sent to MSC/VLR (each used once)

### 45.3 UMTS Mutual Authentication

**Step 3: SIM Verification**
1. SIM computes AK = F5(RAND, Kᵢ)
2. Retrieves SQN = (SQN ⊕ AK) ⊕ AK
3. Checks SQN > stored SQN (freshness)
4. Computes MAC = F1(RAND, Kᵢ, AMF, SQN)
5. If MAC matches received AUTN → network is authenticated
6. SIM updates stored SQN

**Step 4: Cellphone Response**
- SIM computes RES = F2(RAND, Kᵢ)
- Sends RES to network
- Network compares with XRES

### 45.4 UMTS Message Integrity

- **Signalling messages only** (user data not integrity protected)
- **MAC computation**:
```
Per-message MAC = F9(IK, COUNTᵢ, FRESH, DIRECTION, message)
```
where:
- **IK**: Integrity Key (from AKA)
- **COUNTᵢ**: Sequence number (prevents replay)
- **FRESH**: Random number (from BSC)
- **DIRECTION**: 1-bit (uplink/downlink)

### 45.5 UMTS Encryption

- **Stream cipher** for both signalling and user data
- **Keystream generation**:
```
KEYSTREAM = F8(CK, COUNTc, BEARER, DIRECTION, LENGTH)
```
where:
- **CK**: Cipher Key (from AKA)
- **COUNTc**: Frame counter
- **BEARER**: Radio channel indicator

### 45.6 KASUMI Block Cipher

| Property | Details |
|----------|---------|
| **Type** | 64-bit block cipher, 128-bit key |
| **Based on** | MISTY1 (Mitsubishi, proven security) |
| **Hardware** | < 1000 gates |
| **Performance** | ~2 Mbps at 200 MHz clock |
| **Used in** | F8 (encryption) and F9 (integrity) |

---

## Module 46: Wireless Cellular Network Security - Part 4 (4G/LTE Architecture)

### 46.1 Evolution from 3G to 4G

| Feature | 3G (UMTS) | 4G (LTE/EPS) |
|---------|-----------|--------------|
| **Domains** | CS + PS | PS only |
| **MAC** | WCDMA | OFDMA (downlink), SC-FDMA (uplink) |
| **Architecture** | Hierarchical | Flat IP-based |

### 46.2 3G Architecture Recap

**UE (User Equipment)** = ME (Mobile Equipment) + USIM

**Radio Access Network (RAN)**:
- **UTRAN**: WCDMA, Node B (BS), RNC (controller)
- **GERAN**: GSM evolution, BTS, BSC

**Core Network (CN)**:
- **CS Domain**: MSC/VLR, GMSC
- **PS Domain**: SGSN, GGSN
- **Home Network**: HLR/AuC

### 46.3 EPS (Evolved Packet System) - 4G

**Key Goals**:
- Higher data rates
- Lower latency
- High security
- Interworking with legacy systems

**New Elements**:

| Element | Full Form | Function |
|---------|-----------|----------|
| **eNB** | evolved Node B | Single RAN element (replaces Node B + RNC) |
| **MME** | Mobility Management Entity | Control plane (authentication, mobility) |
| **S-GW** | Serving Gateway | User plane anchor |
| **PDN GW** | Packet Data Network Gateway | Traffic to external PDNs |
| **HSS** | Home Subscriber Server | Extended HLR |

---

## Module 47: Wireless Cellular Network Security - Part 5 (EPS Security Architecture)

### 47.1 EPS Security Architecture Overview

**Key Concept**: Hierarchical key derivation from permanent key K

**Authentication Flow**:
1. MME requests authentication data from HSS
2. HSS sends EPS Authentication Vectors
3. MME runs AKA with UE
4. UE and MME share **K_ASME** (Access Security Management Entity key)

### 47.2 Key Distribution in EPS

```
                    K_ASME (MME)
                         |
         +---------------+---------------+
         |                               |
    NAS Protection                  K_eNB (eNB)
    (MME ↔ UE)                           |
                              +----------+----------+
                              |                     |
                         AS Protection         UP Encryption
                         (eNB ↔ UE)            (eNB ↔ UE)
```

### 47.3 Security Services by Layer

| Layer | Protection | Keys |
|-------|------------|------|
| **NAS** (UE ↔ MME) | Integrity + Confidentiality | Derived from K_ASME |
| **AS** (UE ↔ eNB) | Integrity + Confidentiality | Derived from K_eNB |
| **User Plane** (UE ↔ eNB) | Confidentiality only | Derived from K_eNB |
| **S1/X2 Interfaces** | IPsec | Network-specific keys |

### 47.4 Key Features
- **User plane integrity not provided** (performance reasons)
- **IPsec** used for backhaul protection (S1, X2 interfaces)
- **K_eNB** can be renewed without involving MME

---

## Module 48: Wireless Cellular Network Security - Part 6 (EPS AKA)

### 48.1 User Identification

| Identity | Purpose |
|----------|---------|
| **IMSI** | Permanent subscriber identity (identifies key K) |
| **GUTI** | Globally Unique Temporary UE Identity (privacy) |

**Improvement over 3G**: GUTI sent only after NAS security enabled

### 48.2 Terminal Identity

- **IMEI**: International Mobile Equipment Identity (permanent phone ID)
- **Improvement**: In EPS, UE does NOT send IMEI before NAS security activated

### 48.3 EPS AKA Features

**Enhancement over UMTS**:
- **Implicit Serving Network Authentication**
- SN id is input to K_ASME derivation
- HSS verifies SN identity before sending AVs

### 48.4 EPS Authentication Vector Generation

**UMTS AV** (from AuC): <RAND, XRES, CK, IK, AUTN>

**HSS derives K_ASME**:
```
K_ASME = KDF(CK, IK, SN_id, SQN ⊕ AK)
```

**EPS AV** (to MME): <RAND, XRES, K_ASME, AUTN>

### 48.5 Key Differences from UMTS

| Aspect | UMTS | EPS |
|--------|------|-----|
| **AVs per request** | Up to 5 | 1 (recommended) |
| **Key exposed to network** | CK, IK (to SGSN) | K_ASME (to MME only) |
| **Key renewal frequency** | Requires HSS contact | Can renew K_eNB without HSS |
| **SN authentication** | No | Yes (implicit via K_ASME) |

---

## Module 49: Wireless Cellular Network Security - Part 7 (EPS Key Hierarchy)

### 49.1 Complete EPS Key Hierarchy

```
                    K (Permanent Master Key - USIM/AuC)
                              |
                              ↓
                         CK, IK (UMTS keys)
                              |
                              ↓
              K_ASME (Access Security Management Entity)
              (Stored in MME, derived from CK, IK, SN_id)
                              |
         +--------------------+--------------------+
         |                                         |
         ↓                                         ↓
    NAS Keys (MME-UE)                        K_eNB (eNB)
    - K_NASenc (ciphering)                   (Stored in eNB)
    - K_NASint (integrity)                         |
                                                   ↓
                                    AS Keys (eNB-UE)
                                    - K_RRCenc (RRC ciphering)
                                    - K_RRCint (RRC integrity)
                                    - K_UPenc (User Plane encryption)
```

### 49.2 Key Derivation Properties

| Property | Explanation |
|----------|-------------|
| **Key Separation** | Each key used in unique context |
| **One-way Derivation** | Lower keys cannot derive higher keys |
| **Freshness** | Keys can be renewed independently |
| **Limited Exposure** | Lower keys compromised don't expose higher keys |

### 49.3 Benefits of Key Hierarchy

| Benefit | Description |
|---------|-------------|
| **Reduced HSS contact** | K_eNB renewal doesn't need HSS |
| **Handover efficiency** | Keys can be refreshed without MME |
| **Key separation** | Different keys for different contexts |
| **Forward secrecy** | Past sessions safe if current key compromised (with ephemeral DH) |

### 49.4 EPS Security Algorithms (Mandatory)

| Algorithm | Type | Based on |
|-----------|------|----------|
| **128-EEA1, 128-EIA1** | Encryption/Integrity | SNOW 3G (stream cipher) |
| **128-EEA2, 128-EIA2** | Encryption/Integrity | AES |
| **128-EEA3, 128-EIA3** | Encryption/Integrity | ZUC (optional) |

### 49.5 Security Algorithm Negotiation

**Process**:
1. UE sends security capabilities to network
2. Network selects algorithms based on configured priority
3. Network repeats UE capabilities in integrity-protected response
4. UE verifies match → prevents **bidding down attacks**

**Bidding Down Attack**: Attacker modifies UE capabilities to force weak algorithm selection

### 49.6 Protection Summary

| Layer | Integrity | Confidentiality | Replay Protection |
|-------|-----------|-----------------|-------------------|
| **NAS Signalling** | Yes | Yes | Yes |
| **AS Signalling** | Yes | Yes | Yes |
| **User Plane** | No | Yes | No |

---

## Module 50: Wireless Cellular Network Security - Part 8 (5G Security)

### 50.1 5G New Challenges

| Challenge | Description |
|-----------|-------------|
| **Massive IoT** | DDoS attacks on RAN |
| **Edge Computing** | Small cells near users → new attack surfaces |
| **Mission Critical** | Robotic surgery, autonomous vehicles → zero-day vulnerabilities critical |
| **Virtualization** | Network functions virtualized → VM hopping risks |

### 50.2 5G Security Enhancements

| Feature | Description |
|---------|-------------|
| **Network Slicing** | Attacks in one slice don't affect others |
| **Home Control** | Prevents network spoofing; verifies device location in roaming |
| **EAP Native Support** | Plug-in authentication methods; unified for 3GPP/non-3GPP |
| **SEAF** | Security Anchor Function; enables re-authentication without full AKA |

### 50.3 5G Identity Protection

| Identity | Purpose |
|----------|---------|
| **SUPI** | Subscriber Permanent Identifier (like IMSI) - never transmitted |
| **SUCI** | Subscriber Concealed Identifier - temporary, used until authentication |

**Improvement**: Protects against rogue base stations (IMSI catchers)

### 50.4 Edge Computing Security

**Risks**: Edge nodes deployed in unsecured locations

**Defenses**:
- Endpoint protection software
- Monitoring (admin activity, resource usage, performance snapshots)
- Firewalls for DDoS, malware, API protection

### 50.5 Virtualized Infrastructure Security

**Risks**:
- Compromised VNFs (Virtualized Network Functions)
- VM hopping

**Defenses**:
- Block compromised VNFs
- Prevent VM hopping
- Continuous monitoring

### 50.6 Device Security

**For CPE (Customer Premise Equipment) and Small Cells**:
- Encrypt sensitive data stored in non-secure locations
- Cryptographic validation of firmware/software at boot
- Rollback to trusted versions if vulnerabilities detected
- Certificate-based authentication for network access

---

## Quick Reference Tables

### Cellular Network Evolution

| Generation | Technology | Key Security Features | Status |
|------------|------------|----------------------|--------|
| **1G** | Analog | None | Obsolete |
| **2G** | GSM | A3/A5/A8, SIM, TMSI | Still used, insecure |
| **3G** | UMTS | KASUMI, mutual auth, integrity | More secure |
| **4G** | LTE/EPS | Key hierarchy, K_ASME, AES | Current standard |
| **5G** | 5G NR | SUCI, network slicing, EAP | Emerging |

### Key Derivation Functions

| Generation | Key | Derivation |
|------------|-----|------------|
| **GSM** | Kc | A8(RAND, Kᵢ) |
| **UMTS** | CK, IK | F3, F4(RAND, Kᵢ) |
| **EPS** | K_ASME | KDF(CK, IK, SN_id, SQN⊕AK) |
| **EPS** | K_eNB | KDF(K_ASME, counter) |

### Security Comparison

| Feature | GSM | UMTS | EPS (4G) | 5G |
|---------|-----|------|----------|-----|
| **Mutual Authentication** | No | Yes | Yes | Yes |
| **Signalling Integrity** | No | Yes | Yes | Yes |
| **User Plane Integrity** | No | No | No | Optional |
| **Identity Protection** | TMSI | TMSI | GUTI | SUCI |
| **Key Length** | 64-bit | 128-bit | 128-bit | 128/256-bit |
| **Backhaul Security** | No | IPsec | IPsec | IPsec |
| **Network Slicing** | No | No | No | Yes |
| **Home Control** | No | No | No | Yes |

---

**MCQ Tips for Cellular Security:**
- Know **A3/A5/A8** functions in GSM and their purposes
- Understand **why false base station attack** works in GSM but not UMTS
- Remember **KASUMI** is used in UMTS (F8, F9), **AES** in EPS
- Know **key hierarchy** in EPS: K → CK/IK → K_ASME → K_eNB → NAS/AS keys
- **SUCI** in 5G prevents IMSI catching (rogue base stations)
- **Network slicing** provides isolation between services
