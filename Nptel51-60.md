# NPTEL Network Security: Complete Quick Revision Notes (Modules 51-60)

---

## Module 51: Firewalls and Intrusion Detection Systems - Part 1 (Firewall Basics)

### 51.1 What is a Firewall?
- Combination of hardware and software that controls access between internal network and Internet
- **Purpose**: Prevents intruders from attacking internal network and prevents confidential data from leaving

### 51.2 Properties of a Firewall
| Property | Description |
|----------|-------------|
| **All traffic passes through** | Single point of control |
| **Only authorized traffic allowed** | Based on security policy |
| **Hard to compromise** | Unnecessary services removed, patches installed promptly |

### 51.3 Internet Control Message Protocol (ICMP)
- Used for error reporting and network-layer information exchange
- **Protocol field in IP header**: 1 (TCP=6, UDP=17)
- **Examples**:
  - **Redirect**: Tells source host to use a specific router
  - **Ping**: Echo request/response (tests if host is alive)

### 51.4 ICMP-Based Attacks
| Attack | Method |
|--------|--------|
| **Ping sweeps** | Find machines to break into |
| **False unreachable** | Cause host to end connections |
| **ICMP redirect** | Redirect traffic to compromised machine |

### 51.5 Traceroute
- Uses ICMP messages with increasing TTL values
- **Attack use**: Maps internal network configuration → used for later attacks

---

## Module 52: Firewalls and IDS - Part 2 (TCP Fundamentals)

### 52.1 TCP Three-Way Handshake

| Step | Packet | Details |
|------|--------|---------|
| 1 | SYN | Client sends with SYN=1, ISN = client_isn |
| 2 | SYNACK | Server sends with SYN=1, ACK=client_isn+1, ISN=server_isn |
| 3 | ACK | Client sends ACK=server_isn+1, SYN=0 |

**Why random ISN?** Prevents packets from old connections being confused with new ones

### 52.2 TCP Connection Termination
- FIN packet sent by either side
- Requires separate ACK for each FIN (each side closes independently)

### 52.3 SYN Flood Attack (DoS)
- **Attack**: Attacker sends many SYN packets without completing handshake
- **Effect**: Server allocates resources for half-open connections → memory exhaustion
- **Defense**: SYN Cookies

### 52.4 SYN Cookies Defense
```
Cookie = Hash(source IP, dest IP, source port, dest port, secret)
```
- Server sends SYNACK with cookie as initial sequence number
- **No state stored** for half-open connections
- When ACK received, server recomputes cookie to verify
- Only after verification are resources allocated

### 52.5 Domain Name System (DNS)
- Translates hostnames to IP addresses
- **Protocol**: UDP, port 53

---

## Module 53: Firewalls and IDS - Part 3 (Packet Filter Types)

### 53.1 Three Types of Firewalls
1. **Traditional Packet Filters**
2. **Stateful Packet Filters**
3. **Application Gateways**

### 53.2 Traditional Packet Filters
- Examines each packet **in isolation**
- Filtering based on:
  - IP source/destination addresses
  - Protocol type (TCP, UDP, ICMP)
  - TCP/UDP source/destination ports
  - TCP flag bits (SYN, ACK)
  - ICMP message type

### 53.3 Example Filtering Rules

| Policy | Firewall Setting |
|--------|------------------|
| No outside web access | Drop outgoing packets to port 80 |
| Block incoming TCP connections except public web server | Drop incoming SYN packets except to specific IP:port |
| Prevent audio-video traffic | Drop all UDP except DNS |
| Prevent traceroute | Drop outgoing ICMP TTL expired |

### 53.4 Filtering Based on ACK Bit
- First packet of TCP connection: **ACK=0**
- All subsequent packets: **ACK=1**
- **Rule**: Drop incoming TCP packets with ACK=0 → prevents external hosts from initiating connections

---

## Module 54: Firewalls and IDS - Part 4 (Stateful Filters & Application Gateways)

### 54.1 Stateful Packet Filters
- **Tracks TCP connections** using connection table
- Overcomes limitation of traditional filters (attacker sending malformed packets with ACK=1)

**Connection Table Example:**
| Source | Destination | Src Port | Dst Port |
|--------|-------------|----------|----------|
| 222.22.1.7 | 37.96.87.123 | 12699 | 80 |
| 222.22.93.2 | 199.1.205.233 | 7654 | 80 |

### 54.2 Extended Access Control List
- Adds "Check Connection" column
- For UDP traffic: tracks state using source/destination addresses and ports

### 54.3 Application Gateway
- **Application-specific server** through which all application data passes
- Makes decisions based on **application-layer data** (not just headers)
- **Example**: Telnet gateway prompts for username/password before allowing external connection

**Disadvantages:**
- Different gateway needed for each application
- Performance penalty (all data relayed through gateway)

---

## Module 55: Firewalls and IDS - Part 5 (IDS Introduction)

### 55.1 Motivation for IDS
- Packet filters inspect only headers
- Need **deep packet inspection** (application data) to detect:
  - Viruses/worms
  - Application-layer attacks

### 55.2 IDS vs IPS

| Type | Function |
|------|----------|
| **IDS** | Generates alerts for suspicious traffic |
| **IPS** | Filters out suspicious traffic |

### 55.3 Attacks Detectable by IDS
| Attack Type | Description |
|-------------|-------------|
| **Network scanning** | Host discovery, port scanning, OS detection |
| **Vulnerability scanning** | SAINT tool detects weaknesses |
| **OS/Application vulnerability attacks** | Exploiting known flaws |
| **Malware injection** | Worms, viruses |
| **Application-layer DDoS** | Flooding login requests |

### 55.4 Network Architecture with IDS

**DMZ (Demilitarized Zone):**
- Contains external-facing servers (web servers)
- Isolated from internal network
- Most likely to be compromised → isolation protects internal network

**Multiple IDS Sensors:**
- Placed at different points to reduce processing load
- Each sensor sees only fraction of traffic

---

## Module 56: Firewalls and IDS - Part 6 (IDS Classification)

### 56.1 Classification by Detection Method

| Type | Method | Pros | Cons |
|------|--------|------|------|
| **Signature-Based** | Matches packets against attack signatures | Accurate for known attacks | Cannot detect new attacks |
| **Anomaly-Based** | Detects statistically unusual traffic | Can detect new attacks | False positives, difficult to define "normal" |

### 56.2 Signature-Based IDS Example (Snort)
```
alert icmp $EXTERNAL_NET any -> $HOME_NET any 
(msg: "ICMP PING NMAP"; dsize:0; itype:8;)
```
- Detects ICMP ping packets with empty payload (nmap ping sweeps)

### 56.3 False Positives vs False Negatives

| Term | Meaning |
|------|---------|
| **False Negative** | Attack undetected |
| **False Positive** | Alarm raised when no attack |

**Trade-off**: Making signatures more specific reduces false positives but may create false negatives (attacker uses alternative pathnames)

### 56.4 Host-Based vs Network-Based IDS

| Type | Location | Monitors |
|------|----------|----------|
| **Network-Based** | Stand-alone appliance at network points | Packet flows |
| **Host-Based** | Software on host | System calls, file access, logs |

**Host-Based Features:**
- OS logs (logins, failed attempts, commands)
- Application logs
- File system integrity checkers (cryptographic hashes of files)

---

## Module 57: Firewalls and IDS - Part 7 (DDoS Prevention)

### 57.1 SYN Flood Prevention at Host

| Strategy | Description |
|----------|-------------|
| **Source classification** | Drop unfamiliar source addresses under load (may drop legitimate first-time users) |
| **SYN cache** | Store minimal info for half-open connections (reduces memory usage) |
| **SYN cookies** | No state stored (most effective) |

### 57.2 Network-Level DDoS Prevention

**Egress Filtering:**
- Router at network edge drops packets with source addresses not belonging to that network
- Prevents spoofed packets from leaving

**Distributed Route Filtering (DRF):**
- Core routers filter spoofed packets based on source address
- Uses BGP routing information to determine which source addresses should arrive on which interfaces

**Limitations:**
- Requires widespread deployment (≈18% of core routers needed for good coverage)
- BGP route changes may cause legitimate packets to be dropped

---

## Module 58: Firewalls and IDS - Part 8 (SYN Flood Detection Algorithms)

### 58.1 Variables for SYN Flood Detection
- **Sᵢ**: Number of SYN arrivals in interval i
- **Fᵢ**: Number of FIN arrivals in interval i
- **Dᵢ = (Sᵢ - Fᵢ) / Fᵢ**

### 58.2 Detection Algorithms

| Algorithm | Method | Pros | Cons |
|-----------|--------|------|------|
| **Algorithm 1** | Alert if Dᵢ > threshold | Simple | False alarms on spikes; misses cumulative attacks |
| **Algorithm 2** | Alert if exponential average > threshold | Smoother | Threshold tuning difficult |
| **Algorithm 3** | Alert if cumulative sum > threshold | Detects cumulative attacks | More complex |

### 58.3 Algorithm 3 Details
```
u = upper bound on mean of Dᵢ during normal operation
Dᵢ' = Dᵢ - u
Mᵢ = (Mᵢ₋₁ + Dᵢ')⁺ (where x⁺ = max(x,0))
Alert if Mⱼ > threshold
```
- **Advantage**: Detects attacks where Dᵢ stays just below threshold but accumulates over time

---

## Module 59: Tor - Part 1 (Introduction and Motivation)

### 59.1 Problem: Anonymous Communication
Alice wants to:
1. Not reveal her IP address to website
2. Not let local ISP know which website she visits
3. Not let local ISP see data exchanged

### 59.2 Simple Proxy Solution
- Alice connects to trusted proxy via TLS
- Proxy forwards request to website

**Problem**: Proxy knows everything (Alice's IP, destination, and sees all data)

### 59.3 Limitations of TLS and VPN

| Technology | What Intruder Can See |
|------------|----------------------|
| **TLS** | Source and destination IP addresses |
| **VPN** | Communication between networks (reveals collaboration) |

### 59.4 Tor Applications

| User | Purpose |
|------|---------|
| **Individuals** | Prevent tracking, bypass censorship |
| **Journalists** | Communicate with whistle-blowers |
| **NGOs** | Hide organizational affiliations |
| **Business** | Hide market research activities |
| **Military** | Mask field agent locations |

---

## Module 60: Tor - Part 2 (Onion Routing)

### 60.1 Tor Relay Types

| Relay Type | Role |
|------------|------|
| **Entry/Guard Relay** | Entry point to Tor network; knows Alice's IP but not destination |
| **Middle Relay** | Prevents guard and exit from knowing each other |
| **Exit Relay** | Sends traffic to destination; knows destination IP but not source |

### 60.2 Onion Routing Principle
- Data wrapped in **layers of encryption** (like onion layers)
- Each relay can only decrypt its own layer
- No single relay knows complete path

### 60.3 Circuit Establishment (Forward Direction)

```
Alice → Guard:    Encrypted with Guard's key (contains middle relay address)
Guard → Middle:   Decrypts, sees middle address, forwards encrypted packet
Middle → Exit:    Decrypts, sees exit address, forwards encrypted packet
Exit → Bob:       Decrypts final layer, sends plaintext to Bob
```

### 60.4 Data from Bob to Alice
- Exit relay encrypts data (adds one layer)
- Middle relay adds second layer
- Guard relay adds third layer
- Alice decrypts all layers

### 60.5 Security Properties

| Relay | Knows |
|-------|-------|
| **Guard** | Alice's IP (but not destination) |
| **Middle** | Previous and next relay only |
| **Exit** | Destination IP (but not source) |

### 60.6 Limitations

| Limitation | Description |
|------------|-------------|
| **Exit relay can read data** | If not encrypted (use TLS) |
| **End-to-end timing attacks** | Statistical correlation of traffic patterns |
| **User behavior** | User must not reveal identity through data content |

### 60.7 Key Concept: Onion Encryption
- Original data encrypted with exit relay's key
- Then encrypted with middle relay's key
- Then encrypted with guard relay's key
- Each relay removes one layer, revealing next destination

---

## Quick Reference Tables

### Firewall Types Comparison

| Type | Inspection Level | Connection Tracking | Application Aware |
|------|------------------|---------------------|-------------------|
| **Traditional Packet Filter** | Headers only | No | No |
| **Stateful Packet Filter** | Headers only | Yes | No |
| **Application Gateway** | Headers + Application | Yes | Yes |

### IDS Detection Methods

| Aspect | Signature-Based | Anomaly-Based |
|--------|-----------------|---------------|
| **Knowledge required** | Known attack signatures | Statistical baseline |
| **New attacks** | Cannot detect | Can detect |
| **False positives** | Low | High |
| **False negatives** | High for new attacks | Low for new attacks |
| **Deployment** | Common | Less common |

### DDoS Defense Mechanisms

| Layer | Mechanism | Function |
|-------|-----------|----------|
| **Host** | SYN cookies | Prevent resource exhaustion |
| **Network edge** | Egress filtering | Prevent spoofed packets from leaving |
| **Network core** | Distributed Route Filtering | Filter spoofed packets based on source address |

### Tor Properties

| Property | Achieved By |
|----------|-------------|
| **Source anonymity** | Entry relay only knows Alice |
| **Destination anonymity** | Exit relay only knows Bob |
| **Path privacy** | Onion encryption layers |
| **Data confidentiality** | End-to-end encryption (TLS) |

---

**MCQ Tips for Firewalls/IDS:**
- Know **differences** between traditional packet filter, stateful packet filter, and application gateway
- **SYN flood** attack mechanism and **SYN cookies** defense
- **Egress filtering** prevents spoofed packets from leaving a network
- **DMZ** isolates external-facing servers
- **Tor** uses three relays: guard, middle, exit
- **Onion routing** means multiple layers of encryption, each relay removes one layer
