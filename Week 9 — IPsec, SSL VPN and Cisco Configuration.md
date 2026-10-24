

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

