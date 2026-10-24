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


The key distinction is that the VPN endpoints handle the protection. Ordinary internet routers forward the outer packets; they do not need to decrypt the protected inner traffic.

Exam question: What happens at the receiving VPN gateway?

Answer: It decrypts the protected packet, removes the outer encapsulation and forwards the original packet to its destination.


## The concept of a tunnel


A tunnel is a mechanism that puts one protocol's packet inside another protocol's packet.

Think of putting a letter inside an envelope. The envelope helps carry the letter, but it does not automatically make the letter secret.

The lecture gives GRE (Generic Routing Encapsulation) as an example.

GRE can carry different kinds of network traffic inside IP packets. However, ordinary GRE does not inherently encrypt the contents.

Exam point: Tunnelling provides encapsulation, not necessarily security.

## Tunnel vs VPN

Tunnel

- Wraps packets for transport.
    
- Does not automatically provide encryption or authentication.
    
- Example: GRE used without additional security.
    

VPN

- Uses tunnelling or equivalent encapsulation to carry traffic.
    
- Adds security such as encryption, integrity protection and authentication.
    
- Examples: IPsec VPN and SSL/TLS VPN.



## GRE over IPsec

==🟡GRE provides the tunnel, while IPsec provides security for the GRE traffic==. This can be useful when carrying traffic such as dynamic routing protocols or multicast that a plain IPsec setup might not handle in the same way.