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


Memory trick: A tunnel is a container. A VPN is a protected container.

### Tunnel vs VPN comparison

| Feature        | Tunnel                            | VPN                                        |
| -------------- | --------------------------------- | ------------------------------------------ |
| Main purpose   | Encapsulate and transport packets | Secure communication over a public network |
| Encryption     | Not inherent                      | Included as part of VPN protection         |
| Authentication | Not inherent                      | Typically provided                         |
| Examples       | GRE, IP-in-IP                     | IPsec, SSL/TLS VPN                         |
Exam trap: Do not say that every tunnel is encrypted. GRE by itself is not encryption.

## How VPNs are classified

The lecture explains that VPNs are classified using two independent questions:

1. Deployment form: How is the VPN implemented?
    
2. Connection purpose: What is it connecting?
    

These are different dimensions. For example, a remote-access VPN can use software on the user's laptop and a hardware appliance at the company's network edge.

![[Pasted image 20261024180531.png]]

The five VPN types in the lecture are:

VPN deployment form:
1. Software VPN
2. Hardware VPN

VPN connection purpose:

1. Remote Access                              2. Site-to-Site:
     Personal                                           Cloud
     Mobile                             


mportant relationships from the slide:

- Personal VPN is a variant of Remote Access VPN.
    
- Mobile VPN is a variant of Remote Access VPN.
    
- Cloud VPN is mostly a variant of Site-to-Site VPN.



### Software VPN


A software VPN runs as an application or software-based VPN service on an ordinary device or server.

Examples in the lecture include:

- OpenVPN client software.
    
- Cisco AnyConnect client.
    
- Built-in operating system VPN clients.
    
- Software-based VPN servers.
    

Advantages

- Lower upfront cost.
    
- Easy to deploy on many individual devices.
    
- Flexible.
    

Limitation

- Encryption uses the host device's processing resources, so a weak CPU can become a performance bottleneck.


### Hardware VPN

A hardware VPN uses a dedicated physical appliance or a router/firewall with VPN functionality. Some devices include dedicated cryptographic acceleration.

Examples include Cisco routers with VPN support, VPN concentrators and ASA firewalls.

Advantages

- High sustained throughput.
    
- Centralised management at the network edge.
    
- Suitable for handling traffic from many users or an entire site.
    

Limitations

- Higher upfront cost.
    
- Requires suitable hardware and configuration.


Example: A company uses VPN-capable gateways to connect two branch offices.



## Software vs hardware VPN

|Feature|Software VPN|Hardware VPN|
|---|---|---|
|Upfront cost|Lower|Higher|
|Performance|Depends on host CPU|Often higher with crypto acceleration|
|Deployment|Easy across individual endpoints|Centralised at network gateways|
|Typical fit|Individual remote users|Whole-network or high-volume connections|

Memory trick: Software = flexible and cheaper. Hardware = more expensive but designed to handle heavier traffic.

# The five VPN types

The lecture also introduces split tunnelling:

- With split tunnelling, only traffic intended for the organisation's network goes through the VPN.
    
- Other internet traffic can use the normal internet connection.
    
- Depending on configuration, a VPN can instead route all the device's traffic through the tunnel.
    

Advantages: Low cost per user and centralised access control.

Limitations: Depends on the employee's internet connection, requires client software and can introduce risk if a remote device is compromised.

Exam clue: “One employee working from home needs access to the company network” → Remote Access VPN.


## Remote Access VPN analysis