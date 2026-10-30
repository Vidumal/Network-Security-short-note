Memory trick: Cryptography protects the data, not necessarily the network carrying it.


## The four security services

|Security service|Simple question|Main tools in the lecture|
|---|---|---|
|Confidentiality|Can someone else read it?|Symmetric and asymmetric encryption|
|Integrity|Was it changed?|Hash functions and MACs|
|Authentication|Who sent it?|MACs, signatures and certificates|
|Non-repudiation|Can the sender credibly deny signing it?|Digital signatures|

Think of sending a confidential message:

- Confidentiality: strangers cannot read it.
    
- Integrity: you can detect unauthorised changes.
    
- Authentication: you can verify its source.
    
- Non-repudiation: a valid signature can provide evidence that a particular key signed it.
    

Important distinction: encryption alone does not automatically provide integrity, authentication, or non-repudiation.



## Cryptography terminology

Plaintext

Original readable message: HELLO

Encryption + key

Apply the algorithm using a key.

Ciphertext

Scrambled result: KHOOR (Caesar example)

Decryption + key

Recover the original message: HELLO

Learn these four terms:

- Plaintext: readable original data.
    
- Ciphertext: encrypted, scrambled data.
    
- Cipher/algorithm: the mathematical method used.
    
- Key: the value that controls the encryption or decryption.
    

Cryptanalysis means studying or attempting to break cryptographic protection without simply being given the key.

Exam question: “What is the difference between a cipher and a key?”

Answer: The cipher is the method; the key is the value used by that method.




## Kerckhoffs's principle

This is one of the most important concepts in the lecture.

A secure cryptosystem should remain secure even if its algorithm is public. The key must remain secret.

Imagine buying a padlock whose design everyone knows. It can still be secure if it is difficult to open without the correct key.

The same principle applies to cryptography. Security should not depend on hiding the algorithm.

- Use standard, publicly reviewed algorithms such as AES.
    
- Protect the secret keys.
    
- Do not invent your own encryption algorithm for real security.
    
- Do not assume a secret design is automatically secure.
    

Memory trick: Public algorithm, private key.



## Connection to the previous network security lecture

Your earlier IPsec and VPN material already used these cryptographic building blocks:

- AES for confidentiality.
    
- Hash functions such as SHA for hash-related operations.
    
- Pre-shared keys or public-key methods for authentication.
    
- Diffie–Hellman for agreeing on a shared secret.
    

This lecture explains how those building blocks work.

One useful distinction: a hash function is not encryption. Hashing and message authentication are covered more fully in the next lecture.



# Classical ciphers

## Classical ciphers section

Classical ciphers are older encryption methods that are easy to understand and demonstrate by hand. They are not secure enough for modern communications, but they help explain why modern encryption needs a large key space and strong mathematical design.



## Substitution vs transposition

These are two basic ways to transform a message.

Substitution

Replace each symbol with another symbol.

Example: `A → D`

Transposition

Keep the symbols but rearrange their positions.

Example: `TEAM → META`

Memory trick:

- Substitution = replace.
    
- Transposition = shuffle.

## Caesar cipher

The Caesar cipher shifts every letter forward by a fixed number of alphabet positions.

For a key of 3:

|Original|A|B|C|H|E|L|L|O|
|---|---|---|---|---|---|---|---|---|
|Encrypted|D|E|F|K|H|O|O|R|

So:

`HELLO → KHOOR`

To decrypt it, shift every letter backwards by 3.

Why is it symmetric encryption? The sender and receiver use the same secret shift in opposite directions.

Why is it weak? There are only 25 useful nonzero shifts. An attacker can try all 25 possibilities until the message becomes readable. This is a brute-force attack.

Memory trick: Caesar = one fixed shift.

## Why classical ciphers fail

Three important weaknesses appear in the lecture.

1. Tiny key space

Too few possible keys exist. An attacker tries each one until the message makes sense.

Attack to remember: brute force.

2. Frequency analysis

Some letters occur more often than others in natural language. An attacker uses those patterns to infer the original text.

Example: E is common in English.

3. Repeating keyword

Vigenère hides simple letter frequencies, but repeated keyword patterns can reveal its length and allow further analysis.

Named method: Kasiski examination.

## Confusion, diffusion and the avalanche effect

These terms describe important properties of strong cipher design.

- Confusion: makes the relationship between the key and ciphertext complicated.
    
- Diffusion: spreads the influence of plaintext information across the ciphertext.
    
- Avalanche effect: a tiny input change, such as flipping one bit, causes many output bits to change — ideally about half for a well-designed modern cipher.
    

A simple analogy:

Imagine changing one letter in a message. In a strong modern cipher, you should not be able to spot just one corresponding changed ciphertext letter; the effect should spread widely.

|Term|Remember it as|
|---|---|
|Confusion|Complicate the key relationship|
|Diffusion|Spread the patterns|
|Avalanche|Small change, many output changes|

Exam warning: Confusion and diffusion are related but not interchangeable.


# Symmetric encryption

## Slide 16: Symmetric encryption section

The central idea is simple: the sender and receiver share one secret key.

## Slide 17: The symmetric model

Sender

Receiver

Both sides possess the same secret key.

Sender encrypts; receiver decrypts. Ciphertext travels across the untrusted network.

Examples from the lecture:

- AES
    
- DES
    
- 3DES
    
- RC4
    
- ChaCha20
    

They are not all equally secure today. AES and ChaCha20 remain current choices, while DES and RC4 are broken or retired.

Main advantage: symmetric encryption is fast, so it is suitable for protecting large amounts of data.

Main problem: both parties must obtain the same secret key securely.

## The key distribution problem

Suppose Alice wants to send an encrypted message to Bob.

1. Alice and Bob need to share a secret key.
    
2. If Alice sends the key openly over the same untrusted network, an attacker can copy it.
    
3. Once the attacker has the key, the attacker can decrypt traffic protected with it.
    

There is also a scaling problem.

The lecture uses this formula for the number of pairwise shared keys needed by \(n\) users:

\[ \frac{n(n-1)}{2} \]

For example, 10 users need:

\[ \frac{10(10-1)}{2} = 45 \]

100 users need 4,950 pairwise keys.

The number grows rapidly as the number of users increases.

Memory trick: Symmetric encryption is fast, but sharing and managing the keys is difficult.



## Strengths, weaknesses and use cases

|Strengths|Weaknesses|
|---|---|
|Very fast|Secret key must be shared securely|
|Efficient for large amounts of data|Pairwise key management scales poorly|
|Modern CPUs can accelerate AES|A leaked key exposes data protected by it|
|Well suited to bulk encryption|Does not, by itself, provide non-repudiation|

Typical uses include VPN traffic, TLS session data, disk encryption, file encryption, database encryption and Wi-Fi security.

Exam scenario: A company needs to encrypt several gigabytes of data quickly. Symmetric encryption is generally the suitable choice for the data itself.

Why not use only asymmetric encryption? It is much slower for bulk data. The next sections explain how systems solve this problem.


# Block ciphers vs stream ciphers

## Slide 20: Where block and stream ciphers fit

This slide classifies encryption methods into two broad families.

### Block ciphers

Process data in fixed-size blocks.

Examples: DES, 3DES, AES.

### Stream ciphers

Generate a keystream and combine it with the data bit by bit or byte by byte.

Examples: RC4, ChaCha20, A5/1.

Both are traditionally classified as symmetric encryption because they use a shared secret key.

Do not confuse the processing method with the key system:

- Symmetric vs asymmetric: How are keys used?
    
- Block vs stream: How is data processed?
## Block cipher mechanism

A block cipher takes a fixed-size chunk of plaintext and encrypts it with a key.

From your slides:

- DES uses 64-bit blocks.
    
- 3DES uses 64-bit blocks.
    
- AES uses 128-bit blocks.
    

A message may not fit exactly into a block. Some modes therefore require padding to fill the final block. Other modes, such as CTR and GCM, do not require padding.

To encrypt a long message, a block cipher needs a mode of operation. The mode determines how blocks are processed together.

Important: even a strong cipher can be used in an insecure mode.

## ECB vs CBC

### ECB — Electronic Codebook

- Encrypts each block independently.
    
- Identical plaintext blocks produce identical ciphertext blocks under the same key.
    
- Patterns can remain visible.
    

Verdict: Avoid ECB for messages containing multiple blocks.


### CBC — Cipher Block Chaining

- Combines each plaintext block with the previous ciphertext block before encryption.
    
- The first block uses an initialisation vector (IV).
    
- Requires correct padding and IV handling.
    

Verdict: Can be secure when correctly implemented, but modern authenticated modes are generally preferred.
### The easiest way to remember them

ECB = Every block is independent. This makes repeated patterns visible.

CBC = Chain the blocks. Each block depends on the previous ciphertext block.

CBC has an important security risk: careless padding validation can enable a padding oracle attack. This is an attack where different error responses reveal information that helps an attacker decrypt or manipulate ciphertext.



## CTR vs GCM

These modes are especially important for modern network security.

|Feature|CTR|GCM|
|---|---|---|
|Full name|Counter Mode|Galois/Counter Mode|
|How it works|Encrypts counter values to produce a keystream|Combines counter-mode encryption with authentication|
|Padding|Not needed|Not needed|
|Confidentiality|Yes|Yes|
|Integrity protection built in|No|Yes, via an authentication tag|

CTR turns a block cipher into something that behaves like a stream cipher: it generates a keystream and combines it with the plaintext.

GCM adds an authentication tag, providing authenticated encryption when used correctly.


## Block cipher strengths, weaknesses and uses

Strengths

- AES is extensively studied.
    
- Modern processors can accelerate it.
    
- Different modes support different applications.
    
- AES-GCM can provide confidentiality and integrity together.
    

Weaknesses

- ECB leaks patterns.
    
- CBC needs careful padding and IV handling.
    
- CTR and GCM require correct nonce management.
    
- Some modes introduce processing or padding overhead.
    

Use cases

- Full-disk encryption: AES-XTS.
    
- Network traffic: AES-GCM.
    
- Files and databases: appropriate AES-based encryption.
    
- Wi-Fi and VPN traffic: modern AES-based protection.


## Stream cipher mechanism

A stream cipher generates a pseudorandom sequence called a keystream using a secret key and, in typical modern constructions, a nonce.

It combines the keystream with the plaintext using XOR.

For XOR, remember these two simple rules:

- Same bits produce `0`: `0 XOR 0 = 0`, `1 XOR 1 = 0`.
    
- Different bits produce `1`: `0 XOR 1 = 1`, `1 XOR 0 = 1`.
    

Encryption and decryption use the same XOR operation with the same keystream.

The theoretical ideal is the one-time pad: a truly random key as long as the message, used only once. It can provide perfect secrecy, but securely sharing and never reusing such a long key is impractical for most



## RC4 vs ChaCha20 vs A5/1

|Algorithm|Historical or current use|Status|
|---|---|---|
|RC4|WEP and older SSL/TLS systems|Broken; prohibited in TLS|
|ChaCha20|TLS 1.3, WireGuard, SSH|Current, widely recommended|
|A5/1|GSM 2G mobile networks|Broken in practice|

Why was RC4 popular? It was simple and fast. Why is it no longer recommended? Statistical weaknesses in its keystream can leak information about plaintext.

ChaCha20 is designed to perform well in software, including devices without specialised AES hardware. It is commonly paired with Poly1305 for authenticated encryption.

Memory trick: RC4 = retired; A5/1 = old and broken; ChaCha20 = modern option.



## Keystream reuse — the WEP case study

This is one of the most important attack scenarios in this lecture.

A stream cipher must not reuse the same keystream for different messages under the same key.

Why? Suppose:

- Message 1 is encrypted using keystream \(K\).
    
- Message 2 is also encrypted using the same keystream \(K\).
    

Then:

\[ C_1=P_1\oplus K \]

\[ C_2=P_2\oplus K \]

XORing the ciphertexts cancels the repeated keystream:

\[ C_1\oplus C_2=P_1\oplus P_2 \]

The attacker now has a relationship between the two plaintexts. If some plaintext is known or can be guessed, this can help recover other information.

### What happened with WEP?

Your lecture explains that WEP used RC4 with a short, 24-bit initialisation vector (IV). The IV was sent in the clear, and repetition was inevitable on busy networks. WEP also had weaknesses in how the IV was combined with the RC4 key.

This allowed attackers who collected sufficient traffic to recover protected information and, in many cases, the WEP key.

Memory trick: Reused keystream = the same mask used twice. The masks cancel when ciphertexts are XORed.

## Stream cipher strengths, weaknesses and uses

|Strengths|Weaknesses|
|---|---|
|Low latency|Keystream reuse can be catastrophic|
|No padding needed|Basic stream encryption does not provide integrity|
|Suitable for continuously arriving data|A modified ciphertext bit can modify the corresponding plaintext bit|
|Efficient in software|Nonces must be managed correctly|

Use cases include low-latency communications, VPNs, SSH and modern authenticated encryption such as ChaCha20-Poly1305.

A key distinction: stream encryption by itself does not necessarily detect tampering. An attacker may modify ciphertext without knowing the key. Authentication must be added.



## Block vs stream — full comparison

This is a useful exam revision table.

|Feature|Block cipher|Stream cipher|
|---|---|---|
|Processing|Fixed-size blocks|Keystream combined with data|
|Padding|Depends on the mode; some modes require it|Not required|
|Latency|Depends on mode and implementation|Often low|
|Error effects|Depend on the mode|Often local to the affected bit or byte|
|Examples|AES, DES, 3DES|ChaCha20, RC4, A5/1|
|Key risk|Incorrect mode or nonce/IV use|Keystream or nonce reuse|

Do not memorise “all block ciphers need padding.” That is too broad. CTR and GCM are block-cipher modes that do not require padding.



### Which cipher would you choose?

Encrypt an entire laptop disk

Recommended: AES-XTS.

Why: disk sectors need encryption that supports access to individual sectors.

Scenario B: Protect a live voice call

Suitable choices include a stream-style construction such as ChaCha20-Poly1305 or an appropriate AES-based mode.

Why: low latency and continuous data processing matter.

Scenario C: Protect a TLS web session

Suitable choices include AES-GCM or ChaCha20-Poly1305.

Why: both can provide confidentiality and integrity when correctly implemented.





## RSA

RSA is an asymmetric cryptographic algorithm whose security is traditionally based on the difficulty of factoring the product of two large prime numbers.

Suppose:

\[ N=p\times q \]

Multiplying the two primes is straightforward. Recovering the original primes from a sufficiently large, properly generated product is computationally difficult for classical computers.

Your lecture identifies these RSA uses:

- Transporting a small secret key.
    
- Creating digital signatures.
    
- Supporting secure communication protocols.
    

RSA is not normally used to encrypt huge files directly because it is inefficient for bulk data. Instead, it can protect a small key that is then used by a fast symmetric cipher.

The lecture gives 2048-bit RSA as an accepted minimum and 3072 bits as a stronger option for longer-term protection. Actual security depends on correct implementation and the threat model.

Memory trick: RSA = factoring large numbers.





## Diffie–Hellman key agreement

Diffie–Hellman is different from RSA: its main purpose is to let two parties agree on a shared secret across a public channel.

A simplified example:

1. Alice chooses a private value.
    
2. Bob chooses a private value.
    
3. They exchange public values derived from their private values.
    
4. Each combines their own private value with the other party's public value.
    
5. Both calculate the same shared secret.
    

An eavesdropper can see the public exchange but should not be able to calculate the secret efficiently, assuming secure parameters and a sound implementation.

That shared secret can then be used to derive symmetric encryption keys.

Critical limitation: ordinary unauthenticated Diffie–Hellman does not prove who is on the other end. An attacker can perform a man-in-the-middle attack, establishing one secret with Alice and another with Bob.

Certificates or other authentication mechanisms are needed to establish the other party's identity.

Memory trick: Diffie–Hellman = agree on a secret; it does not encrypt the conversation by itself.




## Diffie–Hellman diagram

This slide illustrates the same exchange visually. The essential idea is that both parties independently arrive at the same secret without sending that secret itself across the network.

For the exam, focus on these three points:

- Private values remain secret.
    
- Public values can cross the network.
    
- Authentication must be added to prevent an active attacker from impersonating each party.

## Elliptic Curve Cryptography (ECC)

ECC is another family of asymmetric cryptography.

Instead of relying on the same mathematical problem as RSA, ECC commonly relies on the difficulty of the elliptic-curve discrete logarithm problem.

Its major practical advantage is smaller keys for comparable classical security levels.

The lecture gives this comparison:

- A 256-bit elliptic-curve key provides roughly comparable classical security to a 3072-bit RSA key.
    
- ECDH is used for key agreement.
    
- ECDSA is used for digital signatures.
    
- Curve25519 and P-256 are examples of curves.
    

ECC is attractive for mobile devices and other environments where bandwidth, storage, and processing are limited.

Like RSA and traditional Diffie–Hellman, conventional ECC is vulnerable to a sufficiently capable quantum computer.

Memory trick: ECC = strong classical security with smaller keys.


## RSA vs Diffie–Hellman vs ECC

|Feature|RSA|Diffie–Hellman|ECC|
|---|---|---|---|
|Main purpose|Key transport and signatures|Key agreement|Key agreement and signatures|
|Underlying hard problem|Integer factoring|Discrete logarithm|Elliptic-curve discrete logarithm|
|Approximate 128-bit classical security key size in the lecture|3072 bits|3072 bits|256 bits|
|Forward secrecy|Not by itself|With ephemeral DH|With ephemeral ECDH|
|Main consideration|Larger keys and slower operations|Requires authentication|Correct curve and implementation matter|

Forward secrecy means that compromise of a long-term private key does not automatically expose previously recorded sessions when those sessions used correctly implemented ephemeral key agreement and proper key erasure.

The short exam distinction:

- RSA: factoring.
    
- DH: agree on a secret.
    
- ECC: elliptic curves and smaller keys.




# Symmetric vs asymmetric and hybrid encryption

## Slide 40: Comparison section

This section combines everything you have learned so far. The key exam question is: why do real systems often use both symmetric and asymmetric cryptography?

## Slide 41: Symmetric vs asymmetric — full comparison

|Feature|Symmetric|Asymmetric|
|---|---|---|
|Keys|One shared secret|Public/private key pair|
|Speed|Very fast|Generally much slower|
|Key distribution|Difficult|Public key can be shared openly|
|Keys for \(n\) users|\(n(n-1)/2\) pairwise shared keys|\(n\) key pairs|
|Main use|Encrypting bulk data|Key agreement, key transport and signatures|
|Examples|AES, ChaCha20|RSA, DH, ECC|

One nuance for exams: asymmetric encryption and key agreement are different operations. Diffie–Hellman does not directly encrypt a message; it establishes a shared secret.


## Hybrid encryption

Hybrid encryption combines the strengths of both families.

Phase 1 — Handshake

The parties establish a shared session key using a method such as Diffie–Hellman or ECDH, and authenticate the exchange appropriately.

Purpose: establish keys securely.

Phase 2 — Data transfer

Use a fast symmetric cipher such as AES-GCM or ChaCha20-Poly1305 with the session keys.

Purpose: protect the actual data efficiently.

Why not use only one?

- Asymmetric cryptography helps establish or transport keys, but is expensive for bulk data.
    
- Symmetric cryptography is fast, but the parties need a secure way to establish the shared key.
    
- Hybrid encryption uses each where it is strongest.
    

Memory trick: Asymmetric to set up; symmetric to speed up.




## TLS, IPsec and SSH

Your lecture uses three real-world examples.

|Protocol|Key establishment and authentication|Bulk data protection|
|---|---|---|
|TLS 1.3|ECDHE, certificates and signatures|AES-GCM or ChaCha20-Poly1305|
|IPsec|IKE key exchange and authentication|ESP using AES, often GCM mode|
|SSH|Key exchange and host/user authentication|AES or ChaCha20|

For example, when you visit a website using HTTPS, the browser and server establish session keys during the TLS handshake. The symmetric keys are then used to protect the application data.

Exam point: Hybrid encryption does not mean encrypting every data packet with RSA. It means using asymmetric techniques to establish or protect keys, then symmetric encryption for the bulk data.



