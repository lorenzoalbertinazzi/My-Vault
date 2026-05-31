---
title: Cryptography Fundamentals and Zero-Knowledge Proofs
date: 2026-05-30
tags: [cryptography, zero-knowledge-proofs, ZKP, encryption, public-key, blockchain, privacy, mathematics, AES, RSA, ECC, TLS, PKI, STARKs, Plonk, LWE, post-quantum, hash-functions, digital-signatures, elliptic-curves, symmetric-encryption]
source: "Goldwasser, Micali & Rackoff (1985) The Knowledge Complexity of Interactive Proof Systems; Boneh & Shoup 'A Graduate Course in Applied Cryptography' (2023); Ben-Sasson et al. (2018) STARKs (arXiv:1501.04722); Gabizon et al. (2019) PlonK (arXiv:1912.01216); Bernstein & Lange (2017) Post-quantum cryptography survey (Nature)"
last_updated: 2026-05-31
---

## Summary
Cryptography is the mathematical science of securing information: ensuring confidentiality (only authorized parties can read data), integrity (data hasn't been altered), authentication (identity verification), and non-repudiation (actions can't be denied). Modern cryptography, grounded in computational complexity theory rather than the secrecy of algorithms, has become the invisible infrastructure of the digital world — every HTTPS connection, every password database, every blockchain transaction, every encrypted message depends on it. Zero-Knowledge Proofs (ZKPs), a revolutionary construction first formalized by Goldwasser, Micali, and Rackoff in their seminal 1985 paper, push cryptography to its logical extreme: enabling a **prover to convince a verifier that a statement is true without revealing why it's true** — no information beyond the validity of the claim itself. ZKPs have moved from theoretical computer science curiosity to practical deployed technology in blockchain privacy (Zcash, StarkNet), identity verification, and regulatory compliance, with a growing role in AI model auditing.

## Key Points
- **Kerckhoffs's Principle (1883):** A cryptosystem should be secure even if everything about it except the key is public knowledge — the foundation of modern open-source cryptography
- **Symmetric encryption:** Same key for encryption and decryption (AES-256); extremely fast; key distribution is the fundamental problem
- **Asymmetric (public-key) encryption:** Mathematically linked key pair (RSA, Elliptic Curve); solves key distribution; computationally expensive; ~1000x slower than AES
- **Hybrid encryption:** In practice, asymmetric crypto is used to exchange a symmetric session key; all bulk data encrypted symmetrically (TLS does this)
- **Hash functions:** One-way functions producing fixed-length digests (SHA-256: 256 bits); avalanche effect; collision resistance
- **Zero-Knowledge Proofs:** Interactive or non-interactive protocols proving knowledge/validity without revealing the knowledge itself; defined by Completeness, Soundness, and Zero-Knowledge properties
- **zk-SNARKs (Succinct Non-interactive Arguments of Knowledge):** The most deployed ZKP system; proofs are tiny (~200 bytes) and verify in milliseconds regardless of computation size
- **Post-quantum cryptography:** NIST standardized CRYSTALS-Kyber and CRYSTALS-Dilithium (2024) in response to quantum computing threat to RSA/ECC

## Details

### 1. Mathematical Foundations

Cryptography is built on **computational hardness assumptions** — mathematical problems that are believed to be difficult to solve efficiently (in polynomial time) but easy to verify. Security claims are relative: "This system is secure if problem X is hard." The main hardness assumptions:

**1. Integer Factorization (RSA):**
Given n = p × q where p, q are large primes, find p and q.
- Easy to compute n; believed computationally infeasible to factor for n ≥ 2048-bit numbers on classical computers
- The best classical algorithm (General Number Field Sieve) requires sub-exponential time: O(exp(c × (log n)^(1/3) × (log log n)^(2/3)))
- For 2048-bit RSA: ~10^26 operations — infeasible with current hardware
- **Shor's Algorithm** (quantum, 1994): Factors in polynomial time O((log n)^3) — existential threat to RSA when large-scale quantum computers exist

**2. Discrete Logarithm Problem (DLP):**
Given g, h, p, find x such that g^x ≡ h (mod p).
- Underpins Diffie-Hellman key exchange and DSA
- **Elliptic Curve Discrete Logarithm (ECDLP):** Same problem over elliptic curve groups; far harder per key bit, enabling much shorter keys
- NIST P-256 curve (256-bit key) ≈ security of 3072-bit RSA

**3. Learning With Errors (LWE):**
Given (A, b = As + e) where A is random matrix, s is a secret vector, e is small random noise, find s.
- Believed hard on both classical and quantum computers
- Foundation of post-quantum cryptography (CRYSTALS-Kyber)

### 2. Symmetric Encryption

**AES (Advanced Encryption Standard):**
Selected by NIST in 2001 via public competition (from 15 candidates over 5 years). Designed by Joan Daemen and Vincent Rijmen (Rijndael algorithm). AES is a **substitution-permutation network**:

- Input: 128-bit plaintext block
- Key sizes: 128, 192, or 256 bits
- Rounds: 10 (AES-128), 12 (AES-192), 14 (AES-256)
- Each round: SubBytes (S-box substitution), ShiftRows (cyclic row shift), MixColumns (linear diffusion), AddRoundKey (XOR with round key)

**Performance:** AES-NI hardware instructions (Intel, 2010) enable ~1 byte-per-cycle throughput — essentially free computationally. AES-256-GCM (authenticated encryption) is the standard for TLS 1.3, disk encryption, and secure messaging.

**Block cipher modes:**
- **ECB (Electronic Codebook):** Each block encrypted independently — identical plaintext blocks produce identical ciphertext. Catastrophically insecure (the ECB penguin: encrypting an image in ECB reveals the pixel pattern).
- **CBC (Cipher Block Chaining):** XOR each block with previous ciphertext before encrypting. Better but vulnerable to padding oracle attacks.
- **CTR (Counter Mode):** Converts block cipher to stream cipher; XOR plaintext with encrypted counter values. Parallelizable.
- **GCM (Galois/Counter Mode):** CTR + authentication tag providing both confidentiality and integrity. Dominant mode in TLS.

**The Stream Cipher Alternative:**
ChaCha20-Poly1305 (designed by Daniel Bernstein, 2008): a stream cipher used when AES-NI hardware is unavailable (mobile devices, IoT). TLS 1.3 supports it as an alternative to AES-GCM.

### 3. Asymmetric (Public-Key) Cryptography

**RSA (Rivest, Shamir, Adleman — 1977):**
The first practical public-key cryptosystem. Key generation:
1. Choose large primes p, q (typically 1024–2048 bits each)
2. n = p × q (modulus, made public)
3. φ(n) = (p−1)(q−1) (Euler's totient, kept secret)
4. Choose e such that gcd(e, φ(n)) = 1 (public exponent, typically 65537)
5. Compute d = e⁻¹ mod φ(n) (private exponent)

Encryption: c = m^e mod n
Decryption: m = c^d mod n

**Security relies on:** Given (n, e), finding d requires factoring n — which is believed computationally infeasible for n ≥ 2048 bits.

**RSA in Practice:** RSA is almost never used to encrypt arbitrary data directly. It's used for:
- Key encapsulation: encrypt a random AES key with RSA; use AES for bulk data
- Digital signatures: sign a hash digest (not the full message)
- Certificate authorities: sign TLS certificates (RSA-2048 or ECDSA)

**Elliptic Curve Cryptography (ECC):**
Elliptic curves over finite fields: y² = x³ + ax + b (mod p)

The **discrete logarithm** on elliptic curves is harder than on integer groups, providing equivalent security with much smaller keys:
- 256-bit ECC ≈ 3072-bit RSA
- NIST P-256 (secp256r1): used in TLS, code signing, US government
- Curve25519 (Bernstein): designed for speed and security without NSA influence concerns; used in Signal Protocol, WireGuard, modern TLS
- secp256k1: the curve used in Bitcoin (chosen by Satoshi Nakamoto for performance properties)

**Diffie-Hellman Key Exchange (1976):**
The first public-key protocol, by Whitfield Diffie and Martin Hellman. Allows two parties to establish a shared secret over an insecure channel without pre-shared keys.

```
Alice chooses secret a; sends A = g^a mod p
Bob chooses secret b; sends B = g^b mod p
Shared secret: Alice computes B^a = g^(ab) mod p; Bob computes A^b = g^(ab) mod p
```

Eve sees g, p, A, B but cannot compute g^(ab) without solving the discrete logarithm.

**ECDH (Elliptic Curve DH):** Same protocol using elliptic curve group operations. Used for **forward secrecy** (ephemeral key exchange) in TLS 1.3: even if the server's long-term private key is later compromised, past session keys cannot be recovered.

### 4. Hash Functions and Digital Signatures

**Cryptographic Hash Functions:**
One-way, deterministic functions mapping arbitrary input to fixed-length output:
```
SHA-256("hello") = 2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824
SHA-256("hellp") = 9b71d224bd62f3785d96d46ad3ea3d73319bfbc2890caadae2dff72519673ca7
```

Required properties:
1. **Pre-image resistance:** Given h, infeasible to find x such that H(x) = h
2. **Second pre-image resistance:** Given x, infeasible to find y ≠ x with H(x) = H(y)
3. **Collision resistance:** Infeasible to find any (x, y) with x ≠ y and H(x) = H(y)

**SHA-2 family (NIST, 2001):** SHA-256, SHA-384, SHA-512. Currently secure. The SHA family uses Merkle-Damgård construction (vulnerable to length extension attacks; HMAC wrapping required for message authentication).

**SHA-3 (Keccak, NIST 2015):** Sponge construction; immune to length extension. Used where SHA-2 theoretical weaknesses concern (though SHA-2 has no known practical attacks).

**MD5 (1992) and SHA-1 (1995): Broken.** MD5 collisions found in hours on modern hardware; SHA-1 collision (Google's SHAttered attack, 2017) demonstrated. Not used for security.

**Digital Signatures (ECDSA, EdDSA):**
Signature = Sign(private_key, Hash(message))
Verification = Verify(public_key, message, signature) → True/False

EdDSA (Edwards-curve Digital Signature Algorithm, using Ed25519) is the modern standard: deterministic, fast, immune to implementation pitfalls that plague ECDSA (weak random number generation has broken real-world ECDSA deployments, including PlayStation 3 in 2010).

**PKI (Public Key Infrastructure):**
The trust chain for TLS certificates:
- Root CA (self-signed, in browser trust stores) → Intermediate CA → End-entity certificate
- Certificate contains: public key, domain name, validity period, issuer, signature
- ~170 trusted root CAs exist; any compromise enables man-in-the-middle attacks globally
- Certificate Transparency (CT) logs (Google, 2012) publicly record all issued certificates — detecting unauthorized issuance

### 5. Zero-Knowledge Proofs

**The Definition (Goldwasser, Micali, Rackoff — 1985):**
Shafi Goldwasser, Silvio Micali, and Charles Rackoff published "The Knowledge Complexity of Interactive Proof Systems" in *STOC 1985*, one of the most cited papers in theoretical computer science (Turing Award 2012 for Goldwasser and Micali). They defined a proof system as **zero-knowledge** if it satisfies:

1. **Completeness:** If the statement is true, an honest prover can convince the verifier
2. **Soundness:** If the statement is false, no cheating prover can convince the verifier (except with negligible probability)
3. **Zero-Knowledge:** The verifier learns nothing except that the statement is true — the proof reveals no information about the witness (the secret that makes it true)

**The Ali Baba Cave Analogy (Quisquater et al., 1990):**
The most famous pedagogical example. A cave has a circular path with a door requiring a magic word (password P) to pass.

- The cave has two paths from the entrance (call them L and R) that meet at the locked door
- Prover claims to know P but won't reveal it
- Verifier stands at entrance; Prover walks in (choosing L or R path randomly, out of verifier's sight)
- Verifier calls out randomly: "Come out of the L path" or "Come out of the R path"
- If Prover knows P, they always comply (using the door to switch paths if necessary)
- If Prover doesn't know P, they can only comply 50% of the time (only if they happened to enter the called path)
- After 20 rounds: probability a cheating prover succeeds = (1/2)^20 ≈ 0.000001

The verifier is convinced the Prover knows P, yet has learned nothing about what P is. **Completeness** ✓ **Soundness** ✓ **Zero-Knowledge** ✓

**Graph 3-Coloring ZKP:**
More mathematically concrete. The NP-complete problem: given a graph G, prove it has a valid 3-coloring (assign colors {1,2,3} to nodes such that no adjacent nodes share a color) without revealing the coloring.

Protocol (interactive, per-round):
1. Prover chooses a random permutation σ of the 3 colors and commits to each node's σ-permuted color using a cryptographic commitment scheme (hash locks)
2. Verifier randomly selects an edge (u, v)
3. Prover reveals the committed colors of u and v only
4. Verifier checks: the two colors are different (valid edge) and match the opened commitments
5. Repeat 100+ times

After k rounds, the verifier is convinced (soundness: error ≤ (1 − 1/|E|)^k) and has seen only random permutations of the coloring at random edges — zero-knowledge.

This is foundational because any NP statement can be reduced to graph 3-coloring, proving that ZKPs exist for all of NP.

### 6. zk-SNARKs: The Practical Revolution

**From Interactive to Non-Interactive:**
Interactive proofs require multiple rounds of challenge-response between prover and verifier. Non-interactive ZKPs replace the verifier's challenges with a **Common Reference String (CRS)** — a trusted setup that deterministically encodes all possible challenges. The Fiat-Shamir heuristic (1986) achieves non-interactivity by replacing the verifier's random challenge with a hash of the transcript so far (the "random oracle model").

**zk-SNARKs (Succinct Non-Interactive Arguments of Knowledge):**
Introduced by Groth (2010) and Groth-Sahai; made practical by Pinocchio protocol (Parno et al., 2013) and Groth16 (2016).

Key properties:
- **Succinct:** Proof size is tiny (Groth16: ~200 bytes; Plonk: ~400 bytes) regardless of circuit complexity
- **Non-interactive:** Single round; verifier offline
- **Argument of Knowledge:** Proves knowledge of a witness, not just statement truth
- **Computationally Zero-Knowledge:** Under the Discrete Log assumption on pairing-friendly curves (BN254, BLS12-381)

**Arithmetic Circuit Representation:**
Any computation can be represented as an arithmetic circuit: addition and multiplication gates over a finite field. The prover's claim is: "I know inputs (w₁,...,wₙ) such that this circuit evaluates to true."

**The Trusted Setup Problem:**
zk-SNARKs require a **trusted setup ceremony** producing a CRS. If the ceremony is compromised (someone retains "toxic waste" — the intermediate secret parameters), they can forge proofs. The Zcash team ran a Multi-Party Computation (MPC) ceremony in 2018 with 88 participants: as long as ≥1 participant honestly destroys their portion, the CRS is secure. Powers of Tau ceremonies (used by Plonk) allow universal CRS shared across applications.

**STARKs (Scalable Transparent Arguments of Knowledge):**
Developed by Eli Ben-Sasson (StarkWare, 2018). Key advantages over SNARKs:
- **No trusted setup:** Based on hash functions alone (information-theoretically secure; post-quantum resistant)
- **Transparent:** CRS is public random hash — no ceremony needed
- Disadvantage: Larger proofs (~100KB vs. 200 bytes for SNARKs)

**Plonk (2019):**
A universal SNARK: one trusted setup works for all circuits below a size bound. Eliminates per-application trusted setups. Used by most modern ZK systems (StarkNet, Aztec Network, zkSync).

### 7. Real-World ZKP Applications

**Zcash (2016): Private Cryptocurrency:**
The first production ZKP deployment. In Bitcoin, all transactions are public. In Zcash's shielded transactions, ZKPs prove:
- The sender has sufficient funds (without revealing balance)
- The inputs haven't been double-spent (without revealing which UTXOs are spent)
- The outputs are valid (without revealing recipient or amount)
Proof generation: ~40 seconds on 2016 hardware; ~2 seconds on 2022 hardware (Groth16 with Sapling upgrade).

**Ethereum Layer-2 Scaling:**
The largest production ZKP deployment. Layer-2 rollups process thousands of transactions off-chain, then post a single ZK proof to Ethereum's Layer-1:
- **zkSync (Matter Labs):** Processes ~2,000 TPS vs. Ethereum L1's ~15 TPS; proof verified on-chain in one transaction
- **StarkNet (StarkWare):** Uses STARKs; verifies complex computations (games, DeFi, NFTs)
- **Polygon zkEVM:** ZK-proof of full EVM execution — any Ethereum smart contract can run on it with full zero-knowledge proofs

ZK rollups reduce Ethereum transaction fees by 10–100x while inheriting Ethereum's security.

**ZK Identity Proofs:**
Prove identity attributes without revealing the full identity document:
- "I am over 18" without revealing birthdate or name
- "I am a citizen of country X" without revealing passport details
- "I have a university degree" without revealing which university or when

EU Digital Identity Wallet (eIDAS 2.0, 2024) is building ZKP-based selective disclosure into the standard. Microsoft's Decentralized Identity initiative uses similar approaches.

**ZK Machine Learning (zkML):**
Prove that a neural network inference was computed correctly on specific inputs. Use cases:
- A model provider proves their AI made a specific prediction without revealing the model weights
- Prove that a training dataset satisfied certain properties (no CSAM, no private medical data) without revealing the training set
- Regulatory compliance: prove a financial risk model satisfies regulatory constraints without revealing the model

As of 2025-2026, zkML is early-stage but growing rapidly; projects include EZKL, Giza, and Modulus Labs.

### 8. Post-Quantum Cryptography

**The Quantum Threat:**
Peter Shor's 1994 algorithm demonstrates that a quantum computer with sufficient qubits can factor RSA keys and solve discrete logarithm (breaking ECC) in polynomial time. Google's quantum supremacy claim (2019) and ongoing progress toward fault-tolerant quantum computing mean the threat is real, though timelines are uncertain (~2030–2040 for cryptographically relevant quantum computers per most expert assessments).

**"Harvest now, decrypt later" attacks:** Nation-state adversaries may already be collecting encrypted traffic to decrypt once quantum computers mature. This makes post-quantum migration urgent for long-lived sensitive data.

**NIST Post-Quantum Standardization (completed 2024):**
After a 6-year public process:
- **CRYSTALS-Kyber (ML-KEM):** Key Encapsulation Mechanism (replaces Diffie-Hellman); based on LWE
- **CRYSTALS-Dilithium (ML-DSA):** Digital signatures; based on Module-LWE/Module-SIS
- **SPHINCS+ (SLH-DSA):** Backup signature scheme; hash-based; conservative

Notable: CRYSTALS-Kyber ciphertext: ~768 bytes vs. ECDH: ~32 bytes. The performance cost is modest on modern hardware but matters for IoT.

**TLS and the Migration:**
Major browsers and cloud providers (Google, Cloudflare) began deploying X25519Kyber768 (hybrid classical+PQC key exchange) in TLS in 2023. Hybrid schemes maintain classical security while adding post-quantum protection — insurance for the transition period.

### 9. The Security Landscape: Real-World Failures

**Not math failures — implementation failures:**

- **PlayStation 3 (2010):** ECDSA requires a random k per signature. Sony reused the same k for all PS3 code-signing signatures. Since r = k×G and s = (z + r×d)/k, with fixed k and known r: d = (s×k − z)/r. Private key extracted in minutes.

- **OpenSSL Heartbleed (2014):** Not a cryptographic flaw — a buffer over-read in OpenSSL's heartbeat extension leaked private keys, session tokens, and passwords from ~17% of internet web servers. The vulnerability: a request asking "echo back N bytes" could request up to 64KB even if only 1 byte was sent, reading adjacent memory.

- **Random Number Generator (RNG) failures:** Cryptographic security depends on high-quality randomness. In 2012, researchers found that 0.2% of ~7M RSA public keys shared a prime factor — meaning the RNG during key generation was defective, making private key recovery trivial (gcd attack).

- **Dual EC DRBG (NSA backdoor, revealed 2013 via Snowden):** NIST-standardized random number generator based on elliptic curves; contained parameters that, if the generator was used, allowed the backdoor holder (NSA) to predict all generated "random" numbers. Demonstrated that algorithm choices are not purely mathematical — political and intelligence interests participate.

## Related
- [[machine-learning-fundamentals]] — zkML applications, federated learning privacy
- [[vector-databases-and-embeddings]] — Privacy-preserving embeddings (important emerging area)
- [[agentic-ai-and-multi-agent-systems]] — Cryptographic authentication in multi-agent trust models
- [[docker-and-containerization]] — Secure computing environments, trusted execution
- [[transformer-architecture]] — Homomorphic encryption for private LLM inference (emerging)
