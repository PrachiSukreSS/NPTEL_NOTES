# NPTEL Network Security: Complete Quick Revision Notes (Batch 2)

---

## Module 1: Cryptography Fundamentals

### 1.1 What is Cryptography?
- **Plaintext**: Original message
- **Ciphertext**: Encrypted/disguised message
- **Purpose**: Achieve confidentiality (intruder gains no information from intercepted message)

### 1.2 Caesar Cipher
- Each letter replaced with letter **k** positions later (wraparound)
- **Key**: Shift value k
- **Vulnerability**: Only 25 possible keys → easily broken by brute force

### 1.3 Types of Cryptography

| Type | Key Property | Example |
|------|--------------|---------|
| **Symmetric Key** | Sender & receiver share same key | Caesar cipher, DES, AES |
| **Public Key** | Different keys (public + private) | RSA, Diffie-Hellman |

### 1.4 Monoalphabetic Cipher
- Each plaintext letter maps to **fixed** ciphertext letter throughout message
- **Number of possible keys**: 26! ≈ 4 × 10²⁶
- **Vulnerability**: Can be broken by **frequency analysis** (based on letter frequencies in English text)

### 1.5 Polyalphabetic Cipher
- Multiple monoalphabetic ciphers used; which cipher depends on letter position
- **Advantage**: Cannot be broken by simple frequency analysis
- **Example**: Vigenère Cipher

---

## Module 2: Block Cipher Modes (Encrypting Long Messages)

### 2.1 Electronic Codebook (ECB)
- Each block encrypted independently with same key
- **Problem**: Identical plaintext blocks → identical ciphertext blocks → information leakage

### 2.2 Cipher Block Chaining (CBC)
- **IV (Initialization Vector)**: Random k-bit number sent in plaintext
- **Encryption**: c(i) = K_S(m(i) ⊕ c(i-1))
- **Decryption**: m(i) = K_S(c(i)) ⊕ c(i-1)
- **Advantage**: Identical plaintext blocks produce different ciphertext (high probability)
- **Overhead**: Only one extra block (IV) needs to be sent

### 2.3 Output Feedback Mode (OFB)
- One-time pad generated in advance: b₁, b₂, b₃, ... (by encrypting IV repeatedly)
- Message XORed with as many bits of pad as needed
- **Advantages**:
  - Pad can be pre-generated before message is known
  - Bit errors only affect corresponding bit (no propagation)

### 2.4 Security Warning for OFB and CTR
- **Same IV + same key for different messages** = insecure
- Intruder can XOR two ciphertexts to get XOR of plaintexts

---

## Module 3: Public Key Cryptography - RSA

### 3.1 Key Insight (Diffie-Hellman Breakthrough)
- First time in history: secure communication without pre-shared secret
- Sender and receiver need not meet beforehand

### 3.2 RSA Components

| Component | Description |
|-----------|-------------|
| **Public Key (K⁺)** | (n, e) |
| **Private Key (K⁻)** | (n, d) |

### 3.3 RSA Key Generation Algorithm
1. Choose two large primes **p** and **q**
2. Compute **n = p × q**
3. Compute **z = (p-1) × (q-1)**
4. Choose **e** such that e < n and **gcd(e, z) = 1**
5. Choose **d** such that **(e × d) mod z = 1**

### 3.4 RSA Encryption & Decryption
| Operation | Formula |
|-----------|---------|
| **Encryption** | c = mᵉ mod n |
| **Decryption** | m = cᵈ mod n |

### 3.5 Why RSA Works (Key Theorem)
- **Fermat's Little Theorem**: For prime p, aᵖ mod p = a mod p
- **Bezout's Identity**: For integers a, b with gcd d, ∃ x,y such that ax + by = d
- **Corollary**: If p is prime and a not divisible by p, then aᵖ⁻¹ mod p = 1
- **Result**: mᵉᵈ mod n = m (proved using CRT)

### 3.6 Why e and z Must Be Relatively Prime
- Ensures **d exists** (by Bezout's identity)
- Extended Euclidean algorithm computes d efficiently

### 3.7 RSA Security
- Based on difficulty of factoring n into p and q
- No efficient factoring algorithm known (but not proven impossible)
- **Warning**: If mᵉ < n, intruder can take e-th root to decrypt → solution: padding

### 3.8 Practical Use: Hybrid Approach
- RSA is **slow** (100-10,000× slower than DES)
- **Solution**: Use RSA to exchange symmetric key (K_S), then use DES/AES for bulk data

---

## Module 4: Diffie-Hellman Key Exchange

### 4.1 Purpose
- Allows two parties to agree on shared secret key over insecure channel
- **Cannot** be used for encryption or digital signatures directly

### 4.2 Algorithm Steps

| Step | Alice | Bob |
|------|-------|-----|
| 1 | Chooses secret S_A | Chooses secret S_B |
| 2 | Computes T_A = g^(S_A) mod p | Computes T_B = g^(S_B) mod p |
| 3 | Sends T_A to Bob | Sends T_B to Alice |
| 4 | Computes key = (T_B)^(S_A) mod p | Computes key = (T_A)^(S_B) mod p |

- **Result**: Both compute same key = g^(S_A×S_B) mod p

### 4.3 Security
- Based on **Discrete Logarithm Problem**: Given g, p, and gˣ mod p, finding x is computationally infeasible
- **Requirements for p and g**:
  1. gˣ mod p ≠ 1 unless x is multiple of (p-1)
  2. (p-1)/2 must also be prime ("safe prime")

### 4.4 Man-in-the-Middle Attack
- **Vulnerability**: No authentication; intruder can impersonate both sides
- **Result**: Alice shares key with Mallory, Bob shares key with Mallory
- **Defense**: Use public key infrastructure (PKI) to verify identities

---

## Module 5: Message Integrity & Cryptographic Hash Functions

### 5.1 Message Integrity Problem
- Bob needs to verify:
  1. Alice actually created the message
  2. Message was not modified in transit

### 5.2 Why Simple Checksum Fails
- Intruder can create bogus message with same checksum

### 5.3 Cryptographic Hash Function Properties
| Property | Meaning |
|----------|---------|
| Fixed-length output | e.g., 128, 160, 256 bits |
| Fast computation | Much faster than encryption |
| Pre-image resistance | Infeasible to find m for given H(m) = h |
| Collision resistance | Infeasible to find m ≠ m' with H(m) = H(m') |
| Second pre-image resistance | Given m, infeasible to find m' ≠ m with H(m') = H(m) |

### 5.4 Popular Hash Functions
- **SHA-1**: 160-bit output (collision found in 2017)
- **SHA-2, SHA-3**: More secure versions
- **MD5**: Broken (collisions found 2004-2007)

### 5.5 Message Authentication Code (MAC)
- **Formula**: MAC = H(m, s) where s is shared secret key
- **Process**: Alice sends (m, H(m,s)); Bob verifies
- **Advantage**: No encryption needed (faster)

### 5.6 Digital Signature (Alternative to MAC)
- **Method**: (m, K⁻(H(m))) - sign hash, not entire message
- **Properties**: Verifiable (anyone can verify with public key) and non-forgeable
- **MAC vs Digital Signature**:

| Feature | MAC | Digital Signature |
|---------|-----|-------------------|
| Speed | Fast (no encryption) | Slow (encryption) |
| Shared secret needed | Yes | No |
| Verifiable by anyone | No (only key holder) | Yes |

---

## Module 6: Hash Function Security Analysis

### 6.1 Output Bit Length Requirements

| Attack Type | Success Probability | Required Inputs | Example |
|-------------|---------------------|-----------------|---------|
| **Find m for given h** | High | ~2ˡ | l=64 → 1.8×10¹⁹ inputs (infeasible) |
| **Find collision** | ≥ 1/2 | ~2ˡ/² | l=64 → 2³² inputs (feasible!) |

### 6.2 Birthday Attack (Collision Attack)
- Named after "birthday problem" (probability two people share birthday)
- For l=64, need ~4.3×10⁹ inputs to find collision with high probability
- **Recommendation**: Use l ≥ 128 for collision resistance

### 6.3 Real-World Birthday Attack Example
- Ellen creates 2³² positive letters and 2³² negative letters
- Finds one positive and one negative with same hash
- Dean verifies positive hash with negative letter → attack succeeds

---

## Module 7: SHA-1 Operation

### 7.1 Key Features
- Processes 512-bit blocks
- Output: 160-bit hash (five 32-bit variables H₀-H₄)
- Each block processed in 80 rounds

### 7.2 Padding
- Add "1" bit
- Add zeros to make length multiple of 512
- Append 64-bit message length

### 7.3 Why Padding Matters
- Without proper padding, messages differing only in trailing zeros would have same hash

---

## Module 8: Length Extension Attack

### 8.1 Vulnerability
- **Affects**: SHA-1, SHA-2 when used as H(s, m) or H(m, s) with certain constructions
- **Not affected**: SHA-3 (internal state larger than output)

### 8.2 Attack Example
- Server accepts: `count=10&waffle=eggo` with MAC
- Attacker appends: `&waffle=liege` with proper padding
- Generates valid MAC for modified message without knowing secret key

### 8.3 Secure Construction
- Use **H(s || m)** with SHA-3 (not vulnerable)
- Or use **HMAC** (standardized secure MAC construction)

---

## Module 9: Authentication Protocols

### 9.1 Evolution of Authentication Protocols

| Protocol | Method | Flaw |
|----------|--------|------|
| ap1.0 | "I am Alice" | Anyone can send |
| ap2.0 | Source IP check | IP spoofing |
| ap3.0 | Send password | Password sniffing |
| ap3.1 | Send encrypted password | Replay attack |
| ap4.0 | Nonce + encryption | Works! |

### 9.2 What is a Nonce?
- **Definition**: Number used only once in protocol
- **Purpose**: Detect replay attacks (ensures "liveness")

### 9.3 Protocol ap4.0 (Using Symmetric Key)

**Steps:**
1. Alice: "I am Alice"
2. Bob → Alice: nonce R
3. Alice → Bob: K_{A-B}(R)
4. Bob decrypts and checks if equals R

### 9.4 Authentication Without Encryption (Using Hash)

**Steps:**
1. Alice: "I am Alice"
2. Bob → Alice: nonce R
3. Alice → Bob: H(R, K_{A-B})
4. Bob computes H(R, K_{A-B}) and verifies

### 9.5 Authentication Using Synchronized Clocks (Single Message)

**Method**: Alice sends "I am Alice, K_{A-B}(timestamp)"

**Defenses against attacks:**
- Bob remembers timestamps until they expire
- Use K_{A-B}(Bob|timestamp) to prevent replay to different servers

---

## Module 10: Authentication with Public Keys

### 10.1 Two Approaches

| Approach | Process | Advantage |
|----------|---------|-----------|
| **Sign nonce** | Bob sends R; Alice signs with private key; Bob verifies with public key | No shared secret needed |
| **Encrypt nonce** | Bob sends K⁺(R); Alice decrypts and returns R | No shared secret needed |

### 10.2 Defense Against Server Database Reading Attack
- Server stores only **public keys** (not confidential)
- Intruder who reads database cannot impersonate users

---

## Module 11: Summary Formulas & Key Values

### Important Formulas

| Concept | Formula |
|---------|---------|
| Transmission delay | L/R |
| Propagation delay | d/s |
| RSA encryption | c = mᵉ mod n |
| RSA decryption | m = cᵈ mod n |
| RSA key condition | e × d ≡ 1 mod (p-1)(q-1) |
| Diffie-Hellman shared key | g^(S_A×S_B) mod p |
| MAC | H(m, s) |
| Digital signature | (m, K⁻(H(m))) |

### Recommended Bit Lengths

| Parameter | Recommended |
|-----------|-------------|
| Hash output (collision resistance) | ≥ 128 bits |
| Hash output (pre-image resistance) | ≥ 64 bits |
| RSA modulus n | ≥ 2048 bits |
| Diffie-Hellman prime p | ≥ 2048 bits |

---

## Quick Comparison Table

| Feature | Symmetric Key | Public Key | Hash Function |
|---------|---------------|------------|---------------|
| Keys | Shared secret | Public + Private | None |
| Speed | Fast | Slow | Very fast |
| Use case | Bulk encryption | Key exchange, signatures | Integrity, MAC |
| Example | AES, DES | RSA, Diffie-Hellman | SHA-1, SHA-3 |

---

**Tip for MCQ Exam**: Focus on understanding the **flaws** in early protocols (ap1.0-ap3.1) and how **nonces** and **hash functions** solve them. Remember key formulas for RSA and the security implications of hash output lengths.
