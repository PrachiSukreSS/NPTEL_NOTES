# Network Security - NPTEL Exam Notes

## Module 1: Introduction to Network Security

### Goals of Network Security
- Understand how malicious users attack networks
- Design mechanisms to defend against attacks

### Types of Security Threats
1. **Malware infection** - viruses, worms, trojans
2. **Denial of Service (DoS)** - flooding with bogus packets
3. **Information theft** - passwords, credit card data
4. **Impersonation** - pretending to be legitimate user
5. **Illegal resource access** - free access to paid services
6. **Message modification/replay** - altering or resending messages

---

## Module 2: Communication Networks Basics

### Circuit Switching vs Packet Switching

| Feature | Circuit Switching | Packet Switching |
|---------|------------------|------------------|
| Bandwidth | Reserved end-to-end | Allocated on-demand |
| Call setup | Required | Not required |
| Performance | Guaranteed | Best effort |
| Efficiency | Wastes idle bandwidth | Efficient for bursty traffic |
| Example | Telephone | Internet |

**Key Formula:** 
- For N users sharing 1 Mbps link, each active 10% at 100 kbps
- Circuit switching max N = 10
- Packet switching with N=35: P(11+ simultaneous) ≈ 4 × 10⁻⁴

### Network Performance Metrics

| Metric | Definition |
|--------|------------|
| **Delay** | Time from sender to receiver |
| **Throughput** | Rate of successful data transfer (F/T) |
| **Jitter** | Variation in end-to-end delay |
| **Packet Loss** | Packets dropped by routers |

### Delay Components
1. **Processing Delay** - error checking, routing decision
2. **Queuing Delay** - waiting in buffer (varies with congestion)
3. **Transmission Delay** = L/R (packet length/link bandwidth)
4. **Propagation Delay** = d/s (distance/speed)

**End-to-End Delay** = Sum of all delays on each hop

### TCP Congestion Control
- **End-to-end approach** - no router feedback
- Packet loss → reduce rate
- ACK received → increase rate
- Window size N → avg rate = N/RTT

### MAC Protocols Types
1. **Channel Partitioning** - TDMA, FDMA (static allocation)
2. **Random Access** - nodes transmit randomly, backoff on collision

### Access Networks
| Type | Technology | Speed | Sharing |
|------|------------|-------|---------|
| DSL | Telephone lines | Up to 8 Mbps down, 1 Mbps up | Dedicated |
| Cable | Cable TV | Up to 30 Mbps down | Shared |
| Ethernet | LAN | 10 Mbps - 10 Gbps | Shared/switched |
| Wi-Fi | Wireless LAN | 1 Mbps - several Gbps | Shared |
| Cellular | 2G/3G/4G/5G | Few 10s Mbps | Shared |

### Layered Architecture (Internet)

| Layer | Function | Protocols |
|-------|----------|-----------|
| Application | Network apps | HTTP, FTP, SMTP |
| Transport | Data transfer between apps | TCP, UDP |
| Network | Routing packets | IP, OSPF, RIP |
| Link | Transfer between neighbors | Ethernet, PPP, Wi-Fi |
| Physical | Bit transfer | - |

**Encapsulation:** Each layer adds header to packet from upper layer

---

## Module 3: Types of Attacks

### Malware Types
| Type | Characteristics |
|------|-----------------|
| **Virus** | Attaches to programs, requires human action to spread |
| **Worm** | Standalone, spreads without human action |
| **Trojan** | Hidden in useful program, doesn't self-replicate |
| **Spyware** | Collects and sends secret information |
| **Botnet** | Network of compromised hosts |

### Attack Mechanisms

**Buffer Overflow:**
- Program doesn't check input length
- Writes beyond allocated buffer
- Can overwrite executable code

**SQL Injection:**
- Malicious input alters SQL query structure
- Example: `password' OR '1'='1` makes condition always true

**Packet Sniffing:**
- Passive receiver captures packets on broadcast media
- Defended by encryption

**Traffic Analysis:**
- Analyzing patterns (frequency, length, timing) even if encrypted
- Reveals relationships and behaviors

**IP Spoofing:**
- Creating packets with false source IP address
- Used to attack routing algorithms

---

## Module 4: Cryptography Mathematical Background

### Modulo Arithmetic
- **Definition:** d = n × q + r, 0 ≤ r < n
- **Notation:** r ≡ d (mod n)

**Key Properties:**
```
1) (a + b) mod n = ((a mod n) + (b mod n)) mod n
2) (a - b) mod n = ((a mod n) - (b mod n)) mod n
3) (a × b) mod n = ((a mod n) × (b mod n)) mod n
```

### Greatest Common Divisor (GCD)
- **Definition:** Largest integer dividing both numbers
- **gcd(24,78)** = 6
- **Relatively prime:** gcd(b,c) = 1

### Euclid's Algorithm
```
To find gcd(161,112):
Step 1: 161 = 112 × 1 + 49
Step 2: 112 = 49 × 2 + 14
Step 3: 49 = 14 × 3 + 7
Step 4: 14 = 7 × 2 + 0
∴ gcd(161,112) = 7
```

### GCD Theorem
**For integers b and c, ∃ integers x and y such that:**
```
b × x + c × y = gcd(b,c)
```

**Corollary:** If gcd(b,c) = 1, ∃ x,y such that:
```
b × x + c × y = 1
```

**Application:** To find multiplicative inverse of c modulo prime b:
- Since c×y ≡ 1 (mod b), y is the inverse of c modulo b

### Chinese Remainder Theorem (CRT)
**Statement:** If n₁, n₂, …, nₖ are pairwise relatively prime, and N = n₁ × n₂ × … × nₖ, then:

```
x mod n₁ = x₁
x mod n₂ = x₂
   ⋮
x mod nₖ = xₖ
```

has a **unique solution for x modulo N**

**CRT Formula:**
```
Let aᵢ = N/nᵢ
Let αᵢ = aᵢ⁻¹ mod nᵢ

Then x = (x₁×a₁×α₁ + x₂×a₂×α₂ + ... + xₖ×aₖ×αₖ) mod N
```

**Example:**
N = 210, n₁=5, n₂=6, n₃=7, find f⁻¹(3,5,2)
- a₁=42, α₁=3
- a₂=35, α₂=5
- a₃=30, α₃=4
- x = (3×42×3 + 5×35×5 + 2×30×4) mod 210 = 1493 mod 210 = 23

---

## Quick Revision Formulas

### Delay Calculations
```
Transmission Delay = L/R
Propagation Delay = d/s
End-to-End Delay = Σ(Processing + Queuing + Transmission + Propagation)
```

### Throughput
```
Average Throughput = File Size / Time
Instantaneous Throughput = Rate at current instant
```

### Modulo Operations
```
(a + b) mod n = ((a mod n) + (b mod n)) mod n
(a - b) mod n = ((a mod n) - (b mod n)) mod n
(a × b) mod n = ((a mod n) × (b mod n)) mod n
```

### CRT Formula
```
x = Σ (xᵢ × (N/nᵢ) × (N/nᵢ)⁻¹ mod nᵢ) mod N
```

### Congestion Control
```
Window size N → Send Rate = N / RTT
Packet loss → Reduce window
ACK received → Increase window
```

---

## Key Concepts to Remember

| Concept | Key Point |
|---------|-----------|
| **Confidentiality** | Encryption ensures only intended recipient reads message |
| **Authentication** | Verifying identity of communicating parties |
| **Message Integrity** | Ensuring message not altered in transit |
| **Digital Signature** | Verifiable, non-forgeable electronic signature |
| **Public Key Cryptography** | Enables secure communication without pre-shared keys |
| **VPN** | Extends private network across public network |
| **Firewall** | Filters traffic between networks |
| **IDS** | Detects suspicious network activity |

---

## Common Attack Examples

1. **Stuxnet** - Worm targeting Iran's nuclear centrifuges
2. **Mirai** - Malware infecting IoT devices for DDoS attacks
3. **Russian Election Interference** - Email hacking and leaks
4. **Indian Bank Data Breach** - 3.2 million debit cards compromised

---

## Important Protocols to Know

| Protocol | Purpose |
|----------|---------|
| **SSL/TLS** | Secure TCP connections |
| **IPSec** | Network-layer security, VPNs |
| **802.11i/802.11w** | Wi-Fi security |
| **PGP/S/MIME** | Email security |
| **Tor** | Anonymous connections (onion routing) |

---

**Exam Tips:**
- Memorize delay formulas and units
- Understand CRT calculation steps
- Know differences between circuit vs packet switching
- Remember malware types and characteristics
- Practice modulo arithmetic problems
