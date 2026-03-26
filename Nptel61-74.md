# NPTEL Network Security: Complete Quick Revision Notes (Modules 61-74)

---

## Module 61: Bitcoin Cryptocurrency - Part 1 (Introduction)

### 61.1 What is Bitcoin?
- **Cryptocurrency**: Cryptographic techniques regulate unit generation and transfers
- **Decentralized**: No central authority (bank) controls it
- **Peer-to-peer network** performs:
  - Creation of new bitcoins (mining)
  - Recording all transactions (blockchain)

### 61.2 Key Terminology

| Term | Meaning |
|------|---------|
| **Bitcoin** | The cryptocurrency system |
| **bitcoin** | Units of the cryptocurrency |
| **Blockchain** | Database of all past transactions |
| **Mining** | Process of adding new blocks to blockchain |
| **Miners** | Nodes that perform mining |

### 61.3 Bitcoin Components vs Traditional Banking

| Function | Bitcoin | Traditional Banking |
|----------|---------|---------------------|
| **Address generation** | Public key cryptography | Bank accounts |
| **Ownership proof** | Digital signature | Cheques, passwords, cards |
| **Transaction database** | Blockchain | Bank database |

### 61.4 Blockchain Basics
- Linked list/chain of **blocks**
- **Genesis block**: First block (Jan 2009)
- As of July 2017: ~478,000 blocks, ~125 GB
- Original block size: 1MB; after SegWit (Aug 2017): up to 4MB

### 61.5 Bitcoin Creation Schedule

| Event | Block Reward | Date |
|-------|--------------|------|
| Launch (Jan 2009) | 50 bitcoins | 2009 |
| First halving (block 210,000) | 25 bitcoins | Nov 2012 |
| Second halving (block 420,000) | 12.5 bitcoins | July 2016 |
| Final block (6,930,000) | 0 | ~2140 |

**Total bitcoins that will ever exist**: **21 million**

**Smallest unit**: **satoshi** (1 bitcoin = 100 million satoshi)

---

## Module 62: Bitcoin - Part 2 (Block Structure & Merkle Trees)

### 62.1 Block Header (80 bytes)

| Field | Size | Description |
|-------|------|-------------|
| nVersion | 4 bytes | Block version |
| hashPrevBlock | 32 bytes | Double SHA-256 hash of previous block header |
| hashMerkleRoot | 32 bytes | Root of Merkle tree of transactions |
| nTime | 4 bytes | Timestamp (Unix time) |
| nBits | 4 bytes | Target threshold (encoded) |
| nNonce | 4 bytes | Nonce for mining |

### 62.2 Merkle Tree
- **Purpose**: Efficiently prove a transaction exists in a block
- **Construction**:
  - Leaves: Double SHA-256 hashes of each transaction
  - Parent: Double SHA-256 hash of concatenated child hashes
- **Proof size**: **O(log₂ n)** (where n = number of transactions)

### 62.3 Tamper Resistance
- Any change in a transaction → changes hashMerkleRoot
- Change in hashMerkleRoot → changes block header hash
- hashPrevBlock links to previous block → **entire blockchain integrity**

---

## Module 63: Bitcoin - Part 3 (Mining & Target Threshold)

### 63.1 Mining Process
1. Miner collects transactions from mempool
2. Creates **coinbase transaction** (block reward to miner's address)
3. Forms candidate block
4. Searches for nNonce such that: **SHA-256(SHA-256(block header)) ≤ T**

### 63.2 Target Threshold (nBits)
- Encoded using base 256 scientific notation:
```
T = (mantissa) × 256^(exponent - 3)
```
where:
- b₁ = exponent
- b₂b₃b₄ = mantissa

### 63.3 Mining Probability
```
p = (T + 1) / 2²⁵⁶
```
Average trials needed = **1/p**

### 63.4 Difficulty Adjustment
- **Every 2016 blocks** (~2 weeks)
- Target: 10 minutes per block
- Adjustment formula:
```
T_new = T_old × (actual_time / 2016 × 600 seconds)
```

### 63.5 Mining Performance
| Device | Hash Rate |
|--------|-----------|
| PC | < 100 MH/s |
| ASIC mining rig | Few TH/s |
| Mining datacenter | Thousands of rigs |

---

## Module 64: Bitcoin - Part 4 (Blockchain Forks & Transactions)

### 64.1 Blockchain Forks
- **Cause**: Two miners find valid blocks at same time
- **Resolution**: Nodes switch to **longest branch**
- **Stale blocks**: Abandoned branches; transactions re-added to mempool

### 64.2 Transaction Structure

**Coinbase Transaction:**
- No input (source = block reward)
- Outputs: Amount + Challenge script

**Regular Transaction:**
- Inputs: Previous TXID + output index + response script
- Outputs: Amount + Challenge script

### 64.3 UTXO (Unspent Transaction Outputs)
- Outputs that haven't been spent
- Total bitcoins owned = sum of all UTXOs address can unlock

### 64.4 Transaction Validity
```
Sum of outputs ≤ Sum of inputs
```
Difference = **transaction fee** (paid to miner)

---

## Module 65: Bitcoin - Part 5 (Transaction Fees & Double Spending)

### 65.1 Transaction Fee Determination
- Miners maximize fees → prefer transactions with **high fee rate** (fee/size)
- Fee rate estimates based on:
  - How many blocks before transaction included
  - Desired delay tolerance

### 65.2 Confirmations
| Confirmations | Meaning |
|---------------|---------|
| 1 | Transaction in block at height N |
| m | m-1 blocks appended after block N |

### 65.3 Double Spending Attack

**Attack Steps:**
1. Alice creates two conflicting transactions:
   - t₁: Pay Bob (broadcast)
   - t₂: Pay Alice (secret)
2. Bob waits for m confirmations, then hands over goods
3. Alice continues mining on secret branch containing t₂
4. If secret branch becomes longer, Alice broadcasts it
5. Network switches to t₂ branch → Bob's payment invalid

**Defense**: Bob waits for **enough confirmations** (typically 6 ~ 1 hour)

**Success probability**: Decreases as m increases (assuming attacker <50% hash power)

---

## Module 66: Bitcoin - Part 6 (Blockchain Integrity & 51% Attack)

### 66.1 Blockchain Integrity
- Modifying an old block requires creating longer branch containing modified block
- Probability decreases exponentially with confirmations
- With 50+ confirmations, nearly impossible (attacker <40% hash power)

### 66.2 Why Transactions Can't Be Modified
- Transaction output (recipient) is part of message signed
- Changing recipient would invalidate signature

### 66.3 51% Attacker
An attacker with >50% network hash rate can:
- Launch double spending attacks with probability 1
- Delete transactions from any old block
- Censor transactions (not include specific transactions)
- Mine only empty blocks (no regular transactions)
- Become sole miner, centralizing Bitcoin

**Why unlikely**: Prohibitive cost; attack would make Bitcoin worthless

---

## Module 67: Cloud Security - Part 1 (Cloud Computing Models)

### 67.1 Cloud Computing Definition
- Ubiquitous, convenient, on-demand network access to configurable computing resources
- Rapidly provisioned and released with minimal management

### 67.2 Essential Cloud Characteristics

| Characteristic | Description |
|----------------|-------------|
| **Broad Network Access** | Available over network via standard mechanisms |
| **Rapid Elasticity** | Resources expand/reduce as needed |
| **Measured Service** | Metering for automatic control and optimization |
| **On-Demand Self-Service** | Unilateral provisioning without human interaction |
| **Resource Pooling** | Resources shared among multiple consumers |

### 67.3 Cloud Service Models

| Model | Description | Examples |
|-------|-------------|----------|
| **SaaS** | Application software in cloud | Gmail, Microsoft 365, Salesforce |
| **PaaS** | Platform for customer applications | Google AppEngine, Heroku, Azure |
| **IaaS** | Virtual machines, storage, networks | Amazon EC2, Azure, Google Compute |

---

## Module 68: Cloud Security - Part 2 (Deployment Models & Actors)

### 68.1 Cloud Deployment Models

| Model | Description | Characteristics |
|-------|-------------|-----------------|
| **Public Cloud** | Available to general public | Inexpensive, scalable, lower SLAs |
| **Private Cloud** | Internal IT environment | More control, compliance |
| **Community Cloud** | Shared among similar organizations | Restricted access, shared resources |
| **Hybrid Cloud** | Mix of two or more clouds | Sensitive data private, other data public |

### 68.2 Cloud Actors

| Actor | Role |
|-------|------|
| **Cloud Service Customer (CSC)** | Uses cloud services |
| **Cloud Service Provider (CSP)** | Makes services available |
| **Cloud Auditor** | Independent assessment |
| **Cloud Broker** | Manages use, performance, delivery |
| **Cloud Carrier** | Provides connectivity |

---

## Module 69: Cloud Security - Part 3 (Risks & Countermeasures)

### 69.1 Top Cloud Security Risks

| Risk | Description | Countermeasures |
|------|-------------|-----------------|
| **Data Breaches** | Unauthorized data access | Encryption, access control |
| **Weak IAM** | Identity/access management flaws | Strong authentication, monitoring |
| **Insecure APIs** | API vulnerabilities | Authentication, encryption, monitoring |
| **Account Hijacking** | Stolen credentials | Two-factor authentication, monitoring |
| **Malicious Insiders** | CSP employee threats | Comprehensive supplier assessment |
| **APT** | Long-term undetected access | Threat intelligence |
| **Data Loss** | Permanent data deletion | Redundancy, backups |
| **Insufficient Due Diligence** | Poor CSP selection | Risk analysis, contract scrutiny |
| **Abuse of Services** | Spamming, malware attacks | Stricter registration, monitoring |
| **DoS** | Service unavailability | Threat intelligence, automated defense |
| **Shared Technology** | Isolation weaknesses | Isolated VMs, vulnerability scanning |

---

## Module 70: Cloud Security - Part 4 (Fully Homomorphic Encryption)

### 70.1 What is Homomorphic Encryption?
- Allows computation on **encrypted data**
- Result decrypts to same as computing on plaintext
- Enables **cloud computing without trusting CSP**

### 70.2 Simple FHE Scheme (Conceptual)

**Key Components:**
- Private key: Large odd integer **n**
- Encryption: `c = kn ± e ± b` where:
  - k = large multiple
  - e = small even noise
  - b = plaintext bit (0 or 1)

**Public Key**: List of encryptions of 0

**Decryption**:
- Find nearest multiple of n
- If difference is even → 0; odd → 1

### 70.3 Operations on Encrypted Bits

| Operation | Result |
|-----------|--------|
| **Addition (c₁ + c₂)** | XOR (⊕) of plaintext bits |
| **Multiplication (c₁ × c₂)** | AND (∧) of plaintext bits |
| **Add/subtract 1** | NOT |

### 70.4 Limitations of Simple Scheme
- Noise increases with each operation
- Must keep noise < n/2 for correct decryption
- Ciphertext size doubles with each multiplication
- **Not practical** but illustrates concept

---

## Module 71: IoT Security - Part 1 (Introduction & Applications)

### 71.1 What is IoT?
- Extension of Internet connectivity to **resource-constrained devices**
- Enables **remote monitoring and control**
- **Machine-to-Machine (M2M)** communication

### 71.2 IoT Applications

| Domain | Applications |
|--------|--------------|
| **Agriculture** | Smart irrigation, livestock tracking, drone surveys, robotic farming |
| **Healthcare** | Remote patient monitoring, elderly care, fall detection |
| **Smart Home** | Automatic lighting, HVAC control, security systems |
| **Industrial** | Predictive maintenance, safety systems |
| **Consumer** | Wearables, smart gadgets, pet tracking |
| **Smart Cities** | Traffic control, waste management, smart lighting, parking |
| **Transportation** | Fleet tracking, vehicle monitoring |
| **Government/Military** | Drone swarms, battlefield sensors, asset tracking |

### 71.3 IoT Device Components

| Component | Function |
|-----------|----------|
| **Sensor** | Measures physical parameters |
| **Actuator** | Responds to signals, affects environment |
| **Microcontroller** | Processing and control |
| **Transceiver** | Wireless communication (Wi-Fi, ZigBee, etc.) |
| **Power Supply** | Typically battery |

---

## Module 72: IoT Security - Part 2 (Challenges & Objectives)

### 72.1 IoT Networking Challenges

| Challenge | Description |
|-----------|-------------|
| **Resource constraints** | Limited power, memory, processing |
| **Low cost design** | Compromised security |
| **Sleep periods** | Nodes disabled for long intervals |
| **Scale** | Extremely large number of devices |
| **Traffic patterns** | Small bursts, periodic/random |
| **QoS requirements** | Vary by application (latency, reliability, throughput) |

### 72.2 IoT Access Methods

| Method | Advantages | Disadvantages |
|--------|------------|---------------|
| **Wired** | Reliable | Expensive, not scalable, no mobility |
| **Short-range Wireless** (Wi-Fi, 802.15.4) | Inexpensive, scalable, low power | Low data rates, interference |
| **Wide-area/Cellular** (4G, 5G) | Ubiquitous coverage, mobility | Limited spectrum for IoT |
| **LPWAN** | Low power, long range | Low data rates |

### 72.3 IoT, Fog, and Cloud Architecture

```
Edge Layer: IoT devices → Gateways
Fog Layer: Distributed computing near edge
Core Network: High-performance backbone
Cloud: Centralized storage/processing
```

### 72.4 IoT Security Objectives

| Objective | Implementation |
|-----------|----------------|
| **Restrict access** | Firewalls, authentication |
| **Restrict physical access** | Locks, guards, card readers |
| **Protect components** | Patches, disable unused ports, least privilege |
| **Prevent data modification** | Protect data in transit and at rest |
| **Detect incidents** | Early detection, monitoring |
| **Maintain functionality** | Redundancy, graceful degradation |
| **Restore after incident** | Incident response plan |

---

## Module 73: IoT Security - Part 3 (Hardware Security & Lightweight Crypto)

### 73.1 Tamper Resistance & Detection

| Concept | Description |
|---------|-------------|
| **Tamper Resistance** | Passive protection against attack |
| **Tamper Detection** | Make system aware of unwanted physical access |

**Tamper Resistance Methods:**
- Hardened enclosures, locks, security screws
- Tightly packed components
- Visible evidence seals and tapes

**Tamper Detection Methods:**
- Switches (mercury, magnetic, pressure)
- Sensors (temperature, radiation, voltage, power)
- Circuitry (flexible circuits, resistance wire, fiber optics)

### 73.2 Lightweight Cryptography
- **Purpose**: Secure algorithms for resource-constrained devices
- **Focus**: Symmetric key algorithms and hash functions
- **Approaches**: Efficient implementations + new lightweight designs

### 73.3 Constrained Device Characteristics
- 8 or 16-bit microcontrollers
- Very little RAM and storage
- 802.15.4 radio (20-250 kbps, 127-byte frames)

### 73.4 Lightweight Algorithm Characteristics

| Feature | Description |
|---------|-------------|
| **Simple rounds** | Many iterations of simple operations |
| **Simple operations** | XOR, rotation, 4×4 S-boxes, permutations |
| **Smaller block sizes** | 64 or 80 bits |
| **Smaller key sizes** | 96 or 112 bits |
| **Small security margins** | By design |

### 73.5 Examples
- **Lightweight block cipher**: SEA (Scalable Encryption Algorithm)
- **Lightweight hash function**: PHOTON

---

## Module 74: Post-Quantum Cryptography

### 74.1 Quantum Computer Basics
- Exploits quantum mechanical phenomena
- Uses **qubits** (superposition of 0 and 1)
- **Not faster** for all problems; only specific ones

### 74.2 Quantum Algorithms for Cryptography

| Algorithm | Impact | Mitigation |
|-----------|--------|------------|
| **Grover's Algorithm** | Speeds up brute force (√N instead of N) | Double key size |
| **Shor's Algorithm** | Factors numbers, computes discrete logs | **Breaks RSA, Diffie-Hellman** |

### 74.3 Post-Quantum Cryptography
- Also called: **Quantum-resistant**, **Quantum-safe**
- Algorithms secure against classical + quantum computers
- **NIST standardization**: 80 proposals (2017), final standards released Aug 13, 2024

### 74.4 Post-Quantum Algorithm Families

| Family | Basis |
|--------|-------|
| **Hash-based signatures** | Cryptographic hash functions |
| **Lattice-based cryptography** | Lattice problems |
| **Code-based cryptography** | Error-correcting codes |
| **Multivariate cryptography** | Systems of multivariate polynomials |

### 74.5 Symmetric Cryptography & Quantum
- **Less affected** by quantum computers
- Grover's algorithm requires doubling key size for equivalent security
- Post-quantum symmetric crypto similar to current

---

## Quick Reference Tables

### Bitcoin Key Metrics

| Metric | Value |
|--------|-------|
| Total bitcoins | 21 million |
| Block time target | 10 minutes |
| Difficulty adjustment | Every 2016 blocks (~2 weeks) |
| Genesis block | Jan 2009 |
| Smallest unit | 1 satoshi = 10⁻⁸ bitcoin |

### Cloud Service Models Comparison

| Feature | SaaS | PaaS | IaaS |
|---------|------|------|------|
| **Customer manages** | Data | Data, applications | Data, apps, OS, middleware |
| **Provider manages** | Everything else | Runtime, middleware, OS, hardware | Hardware |
| **Example** | Gmail | Google AppEngine | Amazon EC2 |

### IoT Security Challenges

| Challenge | Mitigation |
|-----------|------------|
| Resource constraints | Lightweight cryptography |
| Physical insecurity | Tamper resistance/detection |
| Large scale | Automated management |
| Diverse requirements | Flexible network access |

### Quantum vs Classical Cryptography

| Algorithm | Classical Security | Quantum Threat | Mitigation |
|-----------|-------------------|----------------|------------|
| **RSA** | Factoring hard | Shor's algorithm | Post-quantum PKE |
| **Diffie-Hellman** | Discrete log hard | Shor's algorithm | Post-quantum PKE |
| **AES** | Brute force 2ⁿ | Grover's: 2ⁿ/² | Double key size |
| **SHA** | Collision 2ⁿ/² | Grover's: 2ⁿ/² | Double output size |

---

**MCQ Tips:**
- **Bitcoin**: Mining difficulty adjusts to maintain 10-minute blocks; total supply 21M; 51% attack can reverse transactions
- **Cloud**: Know SaaS, PaaS, IaaS differences; public vs private vs hybrid
- **IoT**: Resource constraints drive lightweight crypto; fog computing reduces latency
- **Post-quantum**: Shor's algorithm breaks RSA; Grover's algorithm speeds up brute force; symmetric crypto needs double key size
