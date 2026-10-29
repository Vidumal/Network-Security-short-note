

# IPsec VPN

IPsec = Internet Protocol Security.

It protects IP traffic at the network layer. Think of it as security applied to network packets rather than something that each application must implement individually.



## IPsec overview

IPsec operates at **Layer 3** (the network layer) of the OSI model.



Imagine a computer using a browser, email application and file-sharing application. Rather than each application needing its own IPsec implementation, IPsec can protect the IP traffic underneath those applications.


Key points:

- IPsec is a framework, not one single algorithm.
    
- It contains different components for different security functions.
    
- It can provide confidentiality, integrity and authentication, depending on the protocols and configuration selected.
    
- Applications generally do not need to know that IPsec is protecting their traffic.


Exam memory: IPsec = security at the IP/network layer.



## IPsec as a modular framework

A modular framework means that different components perform different jobs, and compatible choices are selected for each job.

Your slide highlights an especially important distinction:

| Component                            | Main function                                             | Does it encrypt?                   |
| ------------------------------------ | --------------------------------------------------------- | ---------------------------------- |
| AH — Authentication Header           | Integrity and data-origin authentication                  | No                                 |
| ESP — Encapsulating Security Payload | Can provide confidentiality, integrity and authentication | Yes, when encryption is configured |
AH does not provide confidentiality. ESP is the common choice when encryption is required.


Think of the components this way:

- AH: Helps verify that protected packet information has not been altered and that it comes from an authenticated source.
    
- ESP: Can lock the data with encryption and also protect its integrity and origin.


ESP and AH are not interchangeable names for the same thing. They perform different security functions.



## Choosing confidentiality and integrity algorithms

These are separate security requirements, even though some modern algorithms combine them.

Example: AES can provide encryption, while a separate integrity mechanism may protect the data against modification. In an authenticated-encryption mode such as AES-GCM, encryption and integrity protection are combined.

Exam trap: Encryption and integrity are different properties. Encryption alone does not necessarily prove that data has not been modified.



## Peer authentication methods

Your slide covers two options:

- PSK (Pre-Shared Key): Both VPN peers are configured with the same secret.
    
- RSA/certificate-based authentication: Public-key cryptography and certificates are used to establish and verify identity.

With a PSK, both ends must be configured consistently. If the key is exposed or poorly managed, the security of the VPN can be compromised.

With certificate-based authentication, each peer's identity can be validated using a certificate and the relevant trust chain.

Memory trick: PSK = same secret on both sides. Certificates = verify identity using public-key cryptography.


Memory trick: PSK = same secret on both sides. Certificates = verify identity using public-key cryptography.


## Diffie–Hellman group choices

Diffie–Hellman (DH) is a key-agreement method. It allows two peers to establish shared secret material over a network without sending the resulting secret directly across it.

The slide explains that different DH groups use different mathematical parameters. Stronger groups generally use larger or stronger parameters, but the actual security depends on the group and implementation, not simply the group number.


The lecture identifies DH Group 14 as a practical minimum for many production configurations, while noting that older or simulated devices may not support it. Follow current organisational and vendor guidance when choosing parameters.

Remember: DH = agree on shared secret material; it is not the data-encryption algorithm itself.



# IPsec: IKE's two phases

IKE (Internet Key Exchange) negotiates security settings and keys for IPsec.

## Phase 1 — Secure the negotiation

The two peers authenticate each other and establish a protected channel for negotiating security settings. The lecture calls this the ISAKMP Security Association (SA).

## Phase 2 — Protect the actual data

The peers negotiate the IPsec Security Associations that specify how real data traffic will be protected.

Why use two phases?

The peers do not need to repeat the full initial authentication every time they refresh the keys used for data protection.

Exam memory trick:

- Phase 1 = secure the relationship between the VPN peers.
    
- Phase 2 = secure the actual data traffic.


## IPsec transport mode vs tunnel mode



## Transport mode

Protects the payload of the original IP packet while retaining its original IP header.

Original IP header

Protected payload

Typically used for host-to-host protection.


## Tunnel mode

Protects the entire original IP packet and places it inside a new outer IP packet.

New outer IP header

Original IP packet

Original header + payload protected

Commonly used for site-to-site VPNs between gateways.


Memory trick:

- Transport = protect the payload.
    
- Tunnel = protect the whole original packet, then wrap it in a new packet.



The slide notes that either AH or ESP can operate in either mode, but ESP tunnel mode is especially common in real-world VPNs.


## IPsec strengths and limitations


Strengths

- Protects IP traffic independently of the application.
    
- Uses mature, widely supported security standards.
    
- Supports different authentication and cryptographic options.
    
- Is suitable for site-to-site and full-network connections.
    

Limitations

- Configuration involves several components that must agree.
    
- AH has problems traversing NAT because NAT changes IP header information that AH protects.
    
- ESP commonly needs NAT Traversal (NAT-T) to work reliably across NAT devices.
    
- IPsec is generally less convenient than browser-based SSL VPN for simple remote access.



What is NAT? Network Address Translation changes IP addresses, and sometimes ports, as traffic passes through a router. That can conflict with some security mechanisms unless they are designed to handle it.



Exam tip: If the question asks why AH struggles with NAT, explain that NAT modifies IP header information, which can cause AH's integrity verification to fail.


# SSL VPN

SSL VPN uses the TLS technology behind HTTPS to establish protected connections. Although the lecture uses the familiar name “SSL VPN,” modern deployments should use supported TLS versions rather than obsolete SSL versions.


## SSL VPN overview

Why is this useful?

- It can work through a standard web browser.
    
- Clientless access may not require dedicated VPN software.
    
- It often works through firewalls and NAT more easily because HTTPS traffic commonly uses TCP port 443.
    
- It can provide access to specific internal applications rather than the entire internal network.


Example: An employee opens a company VPN portal in a browser and accesses an authorised internal web application.

Memory trick: IPsec = network-layer protection; SSL VPN = TLS-based access, often through HTTPS.



## SSL VPN has interchangeable building blocks too



Like IPsec, TLS is not just one fixed algorithm. It uses several cryptographic components selected during negotiation.

These components include:

- Protocol version.
    
- Key exchange.
    
- Data encryption.
    
- Integrity protection.
    
- Identity verification through certificates.


A weak choice in any important part of the configuration can weaken the overall connection.

The difference is how the choices are negotiated: IPsec configuration commonly describes several components separately, while TLS uses a negotiated cipher suite and related protocol parameters.



## SSL VPN's functional blocks

This slide lists the main TLS building blocks. Learn what each one does.

| Building block   | Purpose                                         | Examples from the slide                    |
| ---------------- | ----------------------------------------------- | ------------------------------------------ |
| Protocol version | Defines the TLS protocol rules                  | TLS 1.2, TLS 1.3                           |
| Key exchange     | Establishes shared secret material              | RSA, DHE, ECDHE                            |
| Bulk encryption  | Protects the data being transmitted             | AES-CBC, AES-GCM, ChaCha20                 |
| Integrity/MAC    | Detects modification of protected data          | SHA-256; integrity built into AEAD ciphers |
| Identity/PKI     | Verifies the server and, optionally, the client | X.509 certificates                         |
The slide also lists older options such as SSLv3, TLS 1.0, TLS 1.1, DES, RC4, 3DES, MD5 and SHA-1. These are deprecated or unsuitable for modern secure configurations; do not memorise them as recommended choices.

A few important terms:

- ECDHE: An elliptic-curve Diffie–Hellman ephemeral key exchange method. It can provide forward secrecy.
    
- Forward secrecy: If a long-term private key is compromised later, previously recorded sessions can remain protected, provided ephemeral key exchange and the other required security conditions were used.
    
- X.509 certificate: A digital certificate used to bind an identity to a public key.
    
- AEAD: Authenticated Encryption with Associated Data. It combines encryption and integrity/authenticity protection in one construction.



Memory trick: Version → Key exchange → Encryption → Integrity → Identity.




### Building two example cipher suites

The slide contrasts a weaker and stronger example.

Weaker example — avoid

TLS 1.2 + RSA key exchange + AES-CBC + SHA-1

This combination uses older design choices and lacks the forward-secrecy benefit of ephemeral key exchange. SHA-1 is deprecated for modern security use.

Stronger example — prefer

TLS 1.3 + ECDHE-style key agreement + AES-GCM + integrated authentication

Modern TLS configurations use strong key agreement and authenticated encryption. TLS 1.3 defines its cipher suites differently from TLS 1.2, so this is a conceptual comparison rather than one literal TLS 1.3 cipher-suite name.

The exam lesson is not simply to memorise a list. It is to understand that secure configuration requires suitable choices across all the building blocks.






## SSL VPN building blocks, function by function

For each function, remember the question it answers:

- Protocol version: Which rules govern the session?
    
- Key exchange: How do the peers establish shared secret material?
    
- Bulk encryption: How is the data made unreadable to outsiders?
    
- Integrity/MAC: How can tampering be detected?
    
- Identity/PKI: How do we know we are talking to the intended server or client?



## SSL VPN client access modes


1. Clientless (portal) mode

Uses an ordinary browser to reach a limited set of internal web applications through a secure portal.

Example: An employee opens the company portal to use an internal webmail or business application.



2. Thin client (port-forwarding) mode

Uses a lightweight component to provide access to selected TCP applications without installing a full VPN client.

Example: A user accesses a particular internal TCP service through a browser-assisted connection.


3. Full-tunnel client mode

Uses a complete client, such as Cisco AnyConnect, to provide broader network access through the VPN.

Example: A remote employee needs access to multiple authorised internal services, not just one website.

Memory trick:

- Clientless = browser only.
    
- Thin client = selected applications.
    
- Full tunnel = broad network access.



# IPsec vs SSL VPN

Both technologies solve the same broad problem: protecting communication over an untrusted network. They approach it differently.

### Slide 21 — Direct comparison

|Feature|IPsec VPN|SSL VPN|
|---|---|---|
|Main layer|Network layer (Layer 3)|Uses TLS above the transport layer|
|Client|Often dedicated software|May require only a browser|
|Traffic scope|Can protect IP traffic broadly|Can target specific applications or provide full-tunnel access|
|NAT/firewall traversal|May require NAT-T and careful configuration|Often easier because HTTPS traffic is commonly allowed|
|Access control|Network-level|Can be granular, down to particular applications or URLs|
|Common fit|Site-to-site and full-network remote access|Individual remote access, including browser-based access|

Which one is better? Neither is universally better. The correct choice depends on the requirements.

- Connecting two office networks? IPsec is a common choice.
    
- Giving an employee browser-based access to selected applications? SSL VPN is often a better fit.
    
- Need full network access for an individual? Either may be suitable, depending on the design.



### Cisco VPN configuration

The lecture now moves from concepts to configuration templates.

The three designs are:

1. GRE tunnel.
    
2. IPsec site-to-site VPN.
    
3. GRE over IPsec.
    

Before using commands, understand what each design achieves.




## GRE tunnel concept

GRE creates a logical tunnel interface between two network devices. It encapsulates traffic, but GRE alone does not encrypt it.

Example from your slide:

```
interface Tunnel0
 ip address 10.10.10.1 255.255.255.252
 tunnel source GigabitEthernet0/0
 tunnel destination 203.0.113.10
```

Meaning:

- `interface Tunnel0` — creates or enters a logical tunnel interface.
    
- `ip address ...` — assigns an IP address to that tunnel interface.
    
- `tunnel source ...` — specifies the local source interface.
    
- `tunnel destination ...` — specifies the remote tunnel endpoint's address.
    

The `203.0.113.10` address is an example address used for documentation; it is not a real public endpoint to configure blindly.

Exam point: GRE provides encapsulation and transport, not encryption.



## IPsec site-to-site, step 1: IKE Phase 1 policy

The lecture's example is:

```
crypto isakmp policy 10
 encryption aes
 hash sha256
 authentication pre-share
 group 14
 lifetime 86400
exit
crypto isakmp key MySharedKey address 203.0.113.10
```

This defines Phase 1 negotiation parameters.

- `crypto isakmp policy 10` — defines a policy for the IKE/ISAKMP Phase 1 negotiation in the Cisco syntax used by the slide.
    
- `encryption aes` — selects AES for protecting the negotiation.
    
- `hash sha256` — specifies SHA-256 for the relevant integrity/hash function in this legacy-style configuration.
    
- `authentication pre-share` — uses a pre-shared secret to authenticate peers.
    
- `group 14` — selects a Diffie–Hellman group.
    
- `lifetime 86400` — sets a lifetime of 86,400 seconds, or 24 hours.
    
- `crypto isakmp key ... address ...` — associates the configured pre-shared key with the specified peer address.
    

`MySharedKey` is a placeholder, not a recommended production secret. The two peers must have compatible settings and matching authentication credentials.



## IPsec site-to-site, step 2: Transform set

The example:

```
crypto ipsec transform-set MYSET esp-aes esp-sha256-hmac
```

The transform set defines how the actual data traffic is protected in Phase 2.

- `MYSET` — the transform-set name.
    
- `esp-aes` — selects ESP with AES encryption.
    
- `esp-sha256-hmac` — selects an HMAC-based SHA-256 integrity mechanism in this command syntax.
    

Memory trick: Phase 1 protects negotiation; the transform set defines protection for the data traffic.

Exact syntax and supported algorithms vary by Cisco platform and software version.



## IPsec site-to-site, step 3: Interesting traffic


The example:

```
access-list 100 permit ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255
```

An ACL defines which traffic should be protected by this VPN. The slide calls it interesting traffic.

Here, the rule matches IP traffic travelling from:

- Source network: `192.168.10.0/24`
    
- Destination network: `192.168.20.0/24`
    

The wildcard mask `0.0.0.255` corresponds to a `/24` network.

The important concept is that traffic matching the ACL is selected for IPsec protection. Traffic not matching it follows normal routing, subject to the device's other configuration.

Exam point: The interesting-traffic ACL identifies what should go through the IPsec VPN.


## IPsec site-to-site, step 4: Crypto map

The example:

```
crypto map MYMAP 10 ipsec-isakmp
 set peer 203.0.113.10
 set transform-set MYSET
 match address 100
exit
interface GigabitEthernet0/0
 crypto map MYMAP
```

The crypto map ties the earlier components together.

- `crypto map MYMAP 10 ipsec-isakmp` — creates a crypto-map entry.
    
- `set peer ...` — identifies the remote VPN peer.
    
- `set transform-set MYSET` — selects the transform set for data protection.
    
- `match address 100` — associates the interesting-traffic ACL.
    
- `crypto map MYMAP` under the interface — applies the crypto map to the interface.
    

Remember the configuration sequence:

1. Phase 1 policy.
    
2. Phase 2 transform set.
    
3. Interesting-traffic ACL.
    
4. Crypto map and interface application.
    

The remote peer must have compatible configuration for the tunnel to establish correctly.



## GRE over IPsec

GRE over IPsec combines the strengths of two technologies:

- GRE creates a flexible logical tunnel.
    
- IPsec encrypts and protects the GRE traffic.
    

The lecture's key configuration distinction is that the interesting-traffic ACL must match GRE packets between the tunnel endpoints (IP protocol 47), rather than matching only the original user traffic inside the tunnel.

Why do this?

GRE can carry traffic such as dynamic routing protocols or multicast that may not be handled in the same way by a basic IPsec site-to-site design. IPsec then protects the GRE tunnel across the public network.

Memory trick: GRE = carry it; IPsec = protect it.




# Summary

The lecture's core points are:

- IPsec protects IP traffic using a framework of security components.
    
- AH provides integrity and authentication but no encryption; ESP can provide encryption and integrity protection.
    
- IKE Phase 1 secures negotiation between peers; Phase 2 negotiates protection for actual data traffic.
    
- Transport mode protects the original packet's payload; tunnel mode protects the entire original packet inside a new outer packet.
    
- SSL VPN uses TLS and can support clientless, thin-client and full-tunnel access.
    
- GRE alone is not encrypted; GRE over IPsec combines flexible tunnelling with security.

