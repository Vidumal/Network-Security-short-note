A VPN (Virtual Private Network) creates a secure connection over a public network such as the internet.

- Traffic sniffing: Capturing network traffic to inspect it.
    
- ARP spoofing: Tricking devices on a local network about the association between an IP address and a MAC address.
    
- TCP hijacking: Attempting to take over or interfere with an existing TCP communication session.


Confidentiality means that only authorised parties should be able to read protected information.

| Mechanism      | Simple meaning                                               |
| -------------- | ------------------------------------------------------------ |
| Tunnelling     | Wraps the original packet inside another packet.             |
| Encryption     | Makes protected data unreadable without the appropriate key. |
| Authentication | Checks the identity of the connecting party.                 |

A VPN can provide three important security properties:

- Confidentiality: Others cannot read the protected data.
    
- Integrity: Changes to protected data can be detected.
    
- Authentication: The parties can verify who they are communicating with.


Easy memory trick: VPN = Tunnel + Encryption + Authentication, with integrity protection also commonly provided.


Imagine you want to send packet A. The VPN protects it and carries it inside packet B.

- Original packet: The actual data being sent.
    
- Outer packet: Carries the protected traffic across the public internet.
    
- Remote VPN device: Removes the outer wrapping and restores the original packet.
    

An observer may see that encrypted VPN traffic is travelling between two endpoints, but should not be able to read the protected contents.

Remember the sequence: Authenticate → Encapsulate → Encrypt → Send → Decrypt → Decapsulate.


