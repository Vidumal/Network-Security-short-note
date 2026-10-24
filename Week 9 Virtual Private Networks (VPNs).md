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

This slide evaluates Remote Access VPN rather than introducing another type.

Remember these four factors:

- Hardware/software: Usually software on the user's device; the gateway can be software or hardware.
    
- Throughput: Modest per user, but the gateway must support the total number of simultaneous users.
    
- Limitations: Internet quality and endpoint security affect the connection.
    
- Cost: Relatively low per user.

A Remote Access VPN securely connects an individual remote user to an organisation's private network using a VPN client and gateway. It is suitable for employees working from home or travelling. It has a relatively low per-user cost but depends on the user's internet connection and the security of their device.



Definition: Connects two entire networks, such as a branch office and headquarters, through an encrypted tunnel.

How it works:

1. Each site has a VPN gateway, usually a router or firewall.
    
2. The gateways establish a secure tunnel across the internet.
    
3. Traffic between the sites passes through that tunnel.
    
4. End-user devices normally do not need their own VPN client for this connection.
    

Example: Fifteen retail branches need continuous access to the headquarters' inventory database.

Advantages

- Suitable for whole-site traffic.
    
- Works transparently for devices behind the gateways.
    
- Cost-effective per user when many users share the connection.
    

Limitations

- Requires setup at each site.
    
- Higher upfront equipment cost.
    
- Needs redundancy planning to avoid a single point of failure.


Exam clue: “Two branches need a permanent connection, and all their devices must communicate” → Site-to-Site VPN.




## Site-to-Site VPN analysis

Remember:

- Deployment: Usually VPN-capable hardware at each site.
    
- Throughput: Must handle the aggregate traffic of the entire site.
    
- Limitations: Requires configuration at both ends and is not primarily designed for individual roaming users.
    
- Cost: Higher upfront cost per site, but can be efficient when many users share it.


## Site-to-Site VPN summary

A Site-to-Site VPN creates an encrypted gateway-to-gateway connection between two networks. It is suitable for connecting branch offices to headquarters or a company's network to a data centre. It can support large amounts of continuous traffic, but requires configuration at each site.



# Personal VPN

Definition: A consumer VPN service that protects an individual's internet traffic and may change the apparent public IP address or browsing location.


The goal is generally personal privacy, not access to a particular company's internal network.

Example: A journalist connects to public Wi-Fi at a café and uses a commercial VPN provider to protect traffic between their device and the provider's VPN server.

Advantages

- Can protect traffic on untrusted Wi-Fi.
    
- Can mask the user's public IP address from destination websites by presenting the provider's exit-server address.
    
- May allow the user to appear to connect from another region.
    

Important limitation: You must trust the VPN provider. The provider can potentially see traffic after it exits the VPN tunnel; HTTPS still matters for protecting traffic between your browser and the destination website.

Exam clue: “An individual wants privacy on public Wi-Fi and does not need company resources” → Personal VPN.




## Personal VPN analysis and summary

- Deployment: Usually a commercial provider's software client.
    
- Cost: Often a consumer subscription; free services may involve trade-offs.
    
- Performance: Depends on the provider's server capacity and the user's internet connection.
    
- Limitation: The provider becomes a party you must trust.


Exam answer: A Personal VPN is designed mainly for individual internet privacy, not for connecting two company networks.



# Mobile VPN



Imagine a healthcare worker using a tablet:

1. They connect through hospital Wi-Fi.
    
2. They travel to a patient's home.
    
3. The tablet switches to cellular data.
    
4. The VPN aims to maintain the secure session without forcing a complete restart each time.
    

A normal VPN connection can sometimes be interrupted when the underlying network changes. A Mobile VPN is specifically designed to maintain session continuity during roaming.



Advantages

- Useful for people who move between networks.
    
- Helps maintain access to internal resources during movement.
    

Limitations

- More complex to implement.
    
- Performance depends on the current network.
    
- Maintaining the connection can consume battery power.


Exam clue: “A worker is constantly moving between Wi-Fi and cellular networks but needs an uninterrupted secure session” → Mobile VPN.


## Mobile VPN analysis and summary


Remember these distinctions:

- Remote Access VPN: Connects a remote user to an organisation.
    
- Mobile VPN: Does that while specifically prioritising connection continuity as the user moves between networks.
    

A mobile device alone does not automatically make a VPN a Mobile VPN. The key feature is maintaining a persistent connection across changing networks.


# Cloud VPN


Definition: A Site-to-Site VPN variant that securely connects an organisation's on-premises network to resources in a public cloud.

For example, a company might keep factory systems in its own building but host analytics in a cloud platform.

The company connects its local VPN gateway to a managed VPN gateway in the cloud. The connection commonly uses IPsec.

Advantages

- Connects on-premises systems to cloud resources securely.
    
- Supports continuous network-to-network communication.
    
- The cloud provider manages the cloud-side gateway service.
    

Limitations

- Throughput and cost depend on the cloud provider and selected service tier.
    
- Gateway charges and data-transfer fees may apply.
    
- Configuration can differ between cloud providers.
    

Exam clue: “A company's local data centre must securely connect to its cloud network” → Cloud VPN.





## Cloud VPN analysis and summary


- Deployment: Often a physical gateway on-premises and a managed virtual gateway in the cloud.
    
- Performance: Depends on the provider's gateway tier and network path.
    
- Cost: Gateway charges plus possible data-transfer fees.
    
- Use cases: Hybrid cloud, extending a data centre into the cloud, and disaster recovery.

# All five VPN types side by side
|VPN type|What it connects|Main purpose|
|---|---|---|
|Remote Access|One user to an organisation|Employee remote access|
|Site-to-Site|One network to another|Branch-to-branch communication|
|Personal|One user to a commercial VPN provider|Personal privacy|
|Mobile|A roaming user to a private network|Maintain connectivity while moving|
|Cloud|On-premises network to cloud network|Hybrid-cloud connectivity|


# Summary

The key messages from this lecture are:

1. The internet provides connectivity but does not guarantee confidentiality.
    
2. A tunnel provides encapsulation; a VPN adds security.
    
3. Software versus hardware describes deployment form.
    
4. Remote Access and Site-to-Site are the two main connection-purpose types.
    
5. Personal and Mobile are Remote Access variants; Cloud is mostly a Site-to-Site variant.
    
6. Choose a VPN based on the real requirement, not just the name.


