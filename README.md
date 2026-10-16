# NPTEL Network Security Notes

Concise, exam-oriented revision notes for the NPTEL **Network Security** course. The repository organizes the course material into Markdown files covering networking fundamentals, cryptography, authentication, secure communication, wireless and cellular security, Bitcoin, cloud security, IoT security, and post-quantum cryptography.

> **Educational resource:** These notes are intended for revision and concept review. They should be checked against the official NPTEL lectures, assignments, textbooks, and current standards before being used as an authoritative technical reference.

## Contents

- [Overview](#overview)
- [Repository structure](#repository-structure)
- [Course coverage](#course-coverage)
- [How to use these notes](#how-to-use-these-notes)
- [Topics at a glance](#topics-at-a-glance)
- [Accuracy and terminology notes](#accuracy-and-terminology-notes)
- [Contributing](#contributing)
- [License](#license)

## Overview

This is a documentation-only study repository. It contains no application code, build system, runtime, package manager, or automated test suite. All content is written in Markdown and uses headings, comparison tables, formulas, ASCII diagrams, protocol message flows, and MCQ-oriented reminders.

The notes emphasize:

- Definitions and security goals
- Attack mechanisms and defenses
- Protocol message sequences
- Cryptographic formulas and key hierarchies
- Comparisons between related standards and technologies
- Short revision tables and exam tips

## Repository structure

```text
.
├── Nptel1-10.md    # Modules 1-4: fundamentals, networking, attacks, crypto math
│                   # Also includes revision material for core network-security concepts
├── Nptel11-20.md   # Modules 1-11 in the file headings: cryptography and authentication
├── Nptel21-31.md   # Modules 12-24 in the file headings: authentication, PKI, email, TLS
├── Nptel32-42.md   # IPsec/VPNs and Wi-Fi security
├── Nptel43-50.md   # Cellular security from GSM through 5G
├── Nptel51-60.md   # Firewalls, IDS, DDoS defense, and Tor
└── Nptel61-74.md   # Bitcoin, cloud, IoT, and post-quantum cryptography
```

### File naming note

The filenames represent broad batches, but the headings inside the files are the source of truth for the module numbers. In particular:

- `Nptel11-20.md` contains headings for Modules 1–11 rather than 11–20.
- `Nptel21-31.md` contains headings for Modules 12–24 rather than 21–31.
- The current repository does not contain a dedicated set of notes for Modules 25–31.
- `Nptel1-10.md` overlaps conceptually with the early cryptography material in `Nptel11-20.md`.

## Course coverage

| File | Main subject areas | Module headings found |
|---|---|---:|
| [`Nptel1-10.md`](Nptel1-10.md) | Network-security goals, networking basics, attacks, cryptographic mathematics, formulas | 1–4 |
| [`Nptel11-20.md`](Nptel11-20.md) | Classical and modern cryptography, RSA, Diffie–Hellman, hashes, MACs, signatures, authentication | 1–11 |
| [`Nptel21-31.md`](Nptel21-31.md) | Mutual authentication, Lamport hashes, session keys, KDC, Needham–Schroeder, PKI, PGP, S/MIME, SSL/TLS | 12–24 |
| [`Nptel32-42.md`](Nptel32-42.md) | IPsec, VPNs, IKE, WEP, WPA/TKIP, WPA2/CCMP, management-frame protection | 32–42 |
| [`Nptel43-50.md`](Nptel43-50.md) | GSM, UMTS, LTE/EPS, key hierarchies, AKA, 5G security | 43–50 |
| [`Nptel51-60.md`](Nptel51-60.md) | Firewalls, TCP attacks, IDS/IPS, DDoS detection and prevention, Tor onion routing | 51–60 |
| [`Nptel61-74.md`](Nptel61-74.md) | Bitcoin and blockchain, cloud models and risks, FHE, IoT, lightweight cryptography, post-quantum cryptography | 61–74 |

## How to use these notes

### Quick revision

Start with the relevant batch file and use the section headings to jump to a module. The end of most files contains quick-reference tables, formulas, security comparisons, or MCQ tips.

### Recommended study order

1. Read `Nptel1-10.md` for networking terminology, attack types, delay formulas, and mathematical prerequisites.
2. Study `Nptel11-20.md` for cryptographic primitives and basic authentication protocols.
3. Continue with `Nptel21-31.md` for authenticated key establishment, PKI, email security, and TLS.
4. Use `Nptel32-42.md` through `Nptel51-60.md` for network, wireless, cellular, firewall, IDS, DDoS, and anonymity topics.
5. Finish with `Nptel61-74.md` for distributed ledgers, cloud, IoT, and quantum-resistant cryptography.
6. Validate formulas, historical claims, algorithm status, and current standards against the official course material and current specifications.

### Local viewing

No build step is required. Any Markdown viewer can render the notes. From a clone of the repository, open a file directly or serve the directory with a static Markdown-compatible tool.

```bash
git clone https://github.com/PrachiSukreSS/NPTEL_NOTES.git
cd NPTEL_NOTES
```

## Topics at a glance

### Foundations and cryptography

- Network layers, switching, delay, throughput, congestion control, and MAC protocols
- Malware, buffer overflow, SQL injection, sniffing, traffic analysis, and IP spoofing
- Modular arithmetic, GCD, Euclid’s algorithm, CRT, RSA, and Diffie–Hellman
- Hash functions, birthday attacks, length-extension attacks, MACs, and digital signatures

### Authentication and secure communication

- Nonce-based authentication and mutual authentication
- Reflection attacks and Lamport one-time passwords
- Session-key establishment and key distribution centers
- Needham–Schroeder, certificates, CAs, CRLs, and PKI trust models
- PGP, S/MIME, SSL/TLS handshakes, record protection, and forward secrecy

### Network and wireless security

- IPsec security associations, ESP, transport/tunnel mode, VPNs, and IKE
- WEP weaknesses and the evolution to WPA, WPA2, 802.11w, and WPA3
- GSM, UMTS, LTE/EPS, and 5G authentication and key hierarchies
- Firewalls, stateful filtering, application gateways, IDS/IPS, and DMZs
- SYN floods, SYN cookies, egress filtering, distributed route filtering, and detection algorithms
- Tor relays and onion routing

### Emerging and applied security

- Bitcoin blocks, Merkle trees, mining, forks, UTXOs, double spending, and 51% attacks
- Cloud service/deployment models, actors, risks, and fully homomorphic encryption
- IoT architectures, constrained devices, hardware tamper protection, and lightweight cryptography
- Grover’s and Shor’s algorithms and post-quantum cryptographic families

## Accuracy and terminology notes

Security standards and cryptographic recommendations change over time. Some material is presented in the historical or course-specific terminology used by the notes. Pay particular attention to:

- Deprecated algorithms and protocols such as WEP, TKIP, MD5, SHA-1, and older SSL terminology
- Simplified descriptions of real-world protocols and cryptographic constructions
- Time-sensitive Bitcoin, cellular, cloud, and post-quantum-cryptography facts
- Differences between educational notation and terminology used in current standards

When using these notes for implementation or security decisions, consult current standards, vendor documentation, and reputable security references.

## Contributing

Contributions are welcome for:

- Correcting formulas, terminology, typos, or outdated claims
- Adding missing Modules 25–31
- Improving navigation with a module index or table of contents
- Adding references to official NPTEL lectures and standards
- Clarifying diagrams and protocol flows

Please keep additions concise, structured with Markdown headings, and focused on revision value. Explain substantive corrections in the commit or pull-request description.

## License

No license file is currently included. Unless a license is added, the repository contents should be treated as **all rights reserved**. Obtain permission before redistributing or republishing the notes.
