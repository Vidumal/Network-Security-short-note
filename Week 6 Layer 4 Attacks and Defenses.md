
Mandatory Tasks of Layer 4:
1. Segmentation : Large application data is divided into smaller transportable units.
2. Multiplexing and demultiplexing : Port numbers allow multiple applications to use the same host and help the receiving computer deliver data to the correct application.

3. End-to-end delivery between processes : IP delivers data host-to-host. Layer 4 helps deliver it process-to-process on those hosts.

4. Basic error detection : TCP and UDP have checksum fields to help detect corruption in transit.
5. Sharing the transport layer : Multiple applications can communicate at the same time using ports and transport-layer information.


Multiplexing: handling data from multiple applications for transmission.
Demultiplexing: directing received data to the correct application.

TCP = correctness and reliability
UDP = simplicity and low overhead

A socket is a communication endpoint associated with an IP address and a port number.


TCP connection is distinguished by four values:

1. Source IP address
2. Source port
3. Destination IP address
4. Destination port

Establishment: starting a TCP connection.
Teardown: closing a TCP connection.


# The Three-Way Handshake


1. SYN 
	Client → Server       
	
	The client asks to start a connection and sends its initial sequence number.

2. SYN-ACK
	 Server → Client

	 The server agrees, sends its own initial sequence number, and acknowledges the client's.

3. ACK
    Client → Server

	 The client acknowledges the server's sequence number. The connection can now be established.

What Gets Reserved at Each Stage?

**When the server receives a SYN, it reserves some memory and creates a half-open connection entry. The entry stores connection state, including information held in a Transmission Control Block (TCB).


The sequence is:

1. Client sends SYN.
2. Server reserves a backlog entry and replies with SYN-ACK.
3. Server waits for the client's final ACK.
4. If the ACK arrives, the connection becomes established.
5. If it does not arrive, the half-open entry remains until it times out.

Why is this a weakness? The server uses real resources before it has confirmed that the connection attempt will be completed.

That is the key idea behind the TCP SYN flood attack, which appears later.

Exam memory: SYN arrives → resources reserved → final ACK missing → half-open connection remains.


A normal TCP connection is typically closed through a four-step exchange:

1. One side sends FIN to say it has finished sending.
    
2. The other side sends ACK.
    
3. The other side sends its own FIN when ready.
    
4. The first side sends the final ACK.


Sometimes the middle steps combine into a FIN-ACK.


After sending the final ACK, the initiating side normally enters TIME_WAIT for a period before fully releasing the connection's state. This helps handle delayed duplicate segments and ensures the final ACK can be retransmitted if necessary.


Why does the lecture mention this? Rapidly opening and closing many connections can leave a large number of sockets in TIME_WAIT and consume system resources.

Remember:

- SYN = start.
    
- FIN = finish normally.
    
- RST = reset or abruptly terminate.

FIN and RST are not the same: FIN is part of an orderly shutdown, while RST abruptly aborts a connection.


# TCP-Based Attacks



The next section covers six TCP attacks. They fall into three groups:

- Information gathering: TCP port scans, TCP host sweeps, OS fingerprinting.
    
- Session abuse: TCP hijacking, TCP RST injection.
    
- Resource exhaustion: TCP SYN flood.



## Six Attacks at a Glance

|Attack|Main purpose|
|---|---|
|TCP port scan|Find open, closed, or filtered ports|
|TCP host sweep|Find which hosts are alive|
|OS fingerprinting|Identify the operating system and version|
|TCP hijacking|Take over an existing session|
|TCP RST injection|Force an existing connection to terminate|
|TCP SYN flood|Fill the server's half-open connection backlog|


One-line memory trick:

Ports → Hosts → OS → Take over → Reset → Exhaust.



## TCP Port Scans: How They Work

A port scan sends TCP probes to different ports on a target to discover which services are listening.

Think of a server as a building with many doors. The attacker checks which doors appear open.

A typical TCP SYN probe may receive:

- SYN-ACK: usually indicates an open port.
    
- RST: usually indicates a closed port.
    
- No response or certain ICMP errors: may indicate filtering, packet loss, or another ambiguous result.


The slide mentions several scan types:

- SYN scan: sends SYN and examines the reply without completing the normal handshake.
    
- Connect scan: completes the full TCP handshake.
    
- FIN, Xmas, and NULL scans: use unusual TCP flag combinations; responses depend on the operating system and filtering behavior.


A scan is reconnaissance, not automatically a compromise. Its value is that it tells the attacker which services may be worth investigating next.
## TCP Port Scans: Mitigation

The slide recommends four main controls:

1. Minimise exposed services: disable unnecessary services and block unneeded ports.
    
2. Firewall and IDS/IPS detection: detect bursts of attempts across many ports.
    
3. Consistent filtering behavior: avoid revealing unnecessary information through differing firewall responses.
    
4. Rate limiting: slow excessive connection attempts.
    

Exam memory: Close unused ports + detect scans + rate-limit.

A firewall cannot make every service invisible in every situation, but limiting exposure reduces the information available to an attacker.






## TCP Host Sweeps: How They Work

A host sweep answers a different question from a port scan.

- Port scan: “Which ports are open on this computer?”
    
- Host sweep: “Which computers are alive on this network?”
    

The attacker probes the same port across many IP addresses. For example, they might send a TCP SYN to port 80 at many addresses.

A SYN-ACK or RST response can indicate that a host is reachable, even if the port itself is closed.

Why use TCP instead of ping? Because some networks block ICMP echo requests but still respond to TCP probes.

Easy memory: A host sweep is like checking the same door on every building in a street.
## TCP Host Sweeps: Mitigation

The lecture recommends:

- Minimise exposed services.
    
- Detect scans that target one port across many IP addresses.
    
- Rate-limit suspicious probing.
    
- Segment the network so a compromised device cannot easily scan the entire address space.
    
- Do not rely on ICMP filtering alone.
    

Exam clue: One port, many IP addresses = likely a host sweep.







## OS Fingerprinting: How It Works

OS fingerprinting means identifying the operating system and sometimes its version by observing how a computer responds to network packets.

Different operating systems may have small differences in their TCP/IP behavior, such as:

- Default TTL values.
    
- TCP window sizes.
    
- TCP option ordering.
    
- Responses to unusual or malformed packets.
    

An attacker sends probes and compares the responses with known OS fingerprints.

Simple example: A detective identifies a person by a set of distinctive clues. Similarly, an attacker identifies a system by its network behavior.

Why is it useful to an attacker? If the operating system and version are known, the attacker can research vulnerabilities that affect that particular version.
## OS Fingerprinting: Mitigation

The slide lists these defenses:

1. Keep systems patched. Knowing the OS does not automatically give an attacker a vulnerability to exploit.
    
2. Packet normalization or scrubbing: some firewalls can make certain packet characteristics less distinctive.
    
3. Defense in depth: use multiple security controls rather than relying on hiding the OS.
    

Exam memory: OS fingerprinting → identify OS → patch it and maintain layered defenses.

Important distinction: hiding an OS fingerprint is not a replacement for patching.




## TCP Hijacking: How It Works

TCP hijacking means taking over or injecting data into an existing TCP session between legitimate parties.

Imagine that two people are having a conversation. An attacker attempts to insert messages that the receiver believes came from one of those people.

The lecture describes the following idea:

1. An attacker obtains information about the current TCP sequence numbers, for example by observing traffic on a compromised or shared network.
    
2. The attacker forges a packet with suitable connection information and sequence numbers.
    
3. If the receiver accepts the forged packet as valid TCP data, the attacker may inject traffic into the session.
    

Why can this be possible? TCP uses sequence numbers to track a connection, but TCP alone does not cryptographically authenticate the sender of every packet.

Impact: Session takeover or data injection.

## TCP Hijacking: Mitigation

The lecture recommends:

- TLS: encrypt and authenticate the session so forged application data cannot pass the session's cryptographic checks.
    
- Randomized initial sequence numbers: make prediction-based attacks much harder.
    
- Anomaly monitoring: investigate unusual duplicate acknowledgements or unexpected sequence-number behavior.
    

A key nuance for exams: TLS helps protect the application data against forgery and tampering. It does not make the underlying TCP connection impossible to disrupt.

Memory: Hijacking = join someone else's conversation.






## TCP RST Injection: How It Works

RST stands for Reset. A TCP RST packet is used to abruptly terminate a connection.

In a TCP RST injection attack, an attacker forges a reset packet and sends it to one or both endpoints of an existing connection.

If the receiver accepts the packet as valid, the connection may terminate prematurely.

The lecture describes this as similar to TCP hijacking in its reliance on connection state and sequence numbers, but with a different goal.

- TCP hijacking: take over or inject data into a session.
    
- TCP RST injection: kill a specific session.
    

Example: Instead of secretly joining a phone conversation, the attacker makes the call disconnect.
## TCP RST Injection: Mitigation

The lecture recommends:

1. Randomized sequence numbers.
    
2. Encrypted sessions, such as TLS, to protect session content.
    
3. Monitoring for unusual volumes of TCP resets.
    
4. Application-level automatic reconnection where appropriate.
    

Important exam distinction: TLS protects the data exchanged through a session, but a forged RST can still disrupt the underlying TCP connection if accepted by the TCP stack. Applications may need to reconnect.

Memory: RST injection = force a reset.







## TCP SYN Flood: How It Works

This is one of the most important attacks in this lecture.

A SYN flood sends a large number of TCP SYN requests but does not complete the three-way handshake.

Attacker

Server

1. Attacker sends many SYN packets.

2. Server reserves half-open backlog entries and replies with SYN-ACK.

3. The final ACK never arrives for many requests.

4. The backlog fills, preventing new connections from being accepted.

Attackers may use spoofed or random source IP addresses, so the server's SYN-ACK replies may not reach a genuine client.

Impact: Denial of service against new TCP connections.

A SYN flood does not necessarily terminate every already-established connection. Its direct target is the server's capacity to accept new connections.

Exam memory: SYN flood = many unfinished handshakes → full backlog → legitimate new connections denied.
## TCP SYN Flood: Mitigation

The slide lists four defenses.

|Defense|How it helps|
|---|---|
|SYN cookies|Encodes necessary connection information in the server's SYN-ACK and delays allocating full connection state until the final ACK arrives|
|Increase backlog size|Allows more half-open connections, raising the threshold|
|Reduce SYN-RECEIVED timeout|Releases unfinished entries sooner|
|Firewall/IPS SYN filtering or rate limiting|Limits suspicious SYN traffic before it consumes server backlog resources|
|DDoS protection|Absorbs very large traffic volumes beyond what a single server can handle|

The strongest concept to understand is SYN cookies: instead of allocating the usual full backlog state for every incoming SYN, the server waits for proof that the client can complete the handshake before allocating that state.

Memory: SYN cookies = save the resources until the client proves it can finish.



## How Lightweight UDP Is

This is the transition from TCP attacks to UDP attacks.

TCP maintains connection state and provides reliability features. UDP is deliberately simpler: it sends datagrams without a transport-layer handshake or built-in delivery guarantees.

That simplicity is useful for fast applications, but it also creates opportunities for abuse.

## Just How Light UDP Actually Is

UDP has these characteristics:

- No handshake: it can send data without establishing a TCP-style connection first.
    
- No built-in connection state: UDP itself does not maintain a TCP-style connection record.
    
- No built-in reliability or ordering: datagrams can be lost or arrive out of order.
    
- No built-in flow or congestion control: UDP itself does not adjust sending rates in the way TCP does.
    
- Small header: 8 bytes, compared with TCP's minimum 20 bytes max 60 bytes
    

Common uses include DNS, VoIP, video, and gaming, where low delay can be more important than retransmitting every lost packet.

Exam memory: UDP = lightweight, connectionless, best effort.

One nuance: applications using UDP can implement their own reliability, authentication, or congestion control. UDP simply does not provide those features by itself.




## Four UDP-Based Attacks at a Glance

|Attack|Main purpose|
|---|---|
|UDP port scan|Find open or closed UDP ports|
|UDP host sweep|Find reachable hosts|
|UDP bomb|Crash or destabilize a vulnerable system with malformed packets|
|UDP flood|Overwhelm bandwidth or processing capacity|

Notice the same pattern as the TCP attacks:

- Reconnaissance: port scans and host sweeps.
    
- Malformed-packet attack: UDP bomb.
    
- Resource exhaustion: UDP flood.





## UDP Port Scans: How They Work

UDP port scanning is harder to interpret than TCP port scanning because UDP has no handshake.

An attacker sends UDP probes to different ports.

Possible results:

- ICMP Port Unreachable: commonly indicates that the UDP port is closed.
    
- A response from the application: indicates that a service is responding.
    
- No response: ambiguous. The port may be open, or the packet or response may have been filtered or lost.
    

For example, a probe to a DNS service might need to contain a valid DNS query to receive a useful response.

Memory: UDP port scan = send UDP probes and interpret replies—or silence—to infer port state.
## UDP Port Scans: Mitigation

The lecture recommends:

1. Rate-limit ICMP Port Unreachable responses.
    
2. Use IDS/IPS to detect scan patterns.
    
3. Minimise exposed UDP services.
    

Why rate-limit ICMP errors? Those responses can give scanners a clear signal that a port is closed. Rate-limiting reduces the amount of information exposed through repeated errors.

Exam memory: Rate-limit ICMP errors + detect scans + close unnecessary UDP ports.

## Slide 32 — UDP Host Sweeps: How They Work and Mitigation

A UDP host sweep sends UDP probes across a range of IP addresses to discover which hosts are reachable.

A host may reveal that it is alive by responding from an application, or by returning an ICMP Port Unreachable message for a closed UDP port.

The attack can work even where ICMP echo requests—ordinary ping—are blocked.

The slide recommends:

- Rate-limit ICMP responses.
    
- Detect horizontal scan patterns.
    
- Minimise exposed services.
    
- Segment the network.
    

Difference to remember:

- UDP port scan: many ports, usually one host.
    
- UDP host sweep: many hosts, often one port or service.




## UDP Bomb Attack: How It Works

A UDP bomb in this lecture means a deliberately malformed UDP packet intended to crash or destabilize a vulnerable network stack.

For example, the packet may contain a UDP length field that does not match the actual payload size. A vulnerable implementation might process the inconsistent information incorrectly.

The intended result is a crash or instability, causing denial of service.

This resembles older malformed-packet attacks discussed elsewhere in the module, but here the focus is UDP packet structure.

Memory: UDP bomb = malformed packet → vulnerable system processes it incorrectly → crash or instability.

This is different from a UDP flood: a UDP bomb abuses malformed packet handling, while a UDP flood overwhelms a system with a large volume of traffic.
## UDP Bomb: Mitigation

The lecture recommends:

1. Patch operating systems and network-device firmware.
    
2. Validate packets at firewalls or IPS devices so malformed traffic can be dropped before reaching vulnerable systems.
    
3. Pay attention to older embedded and IoT devices that may remain unpatched for long periods.
    

Exam memory: UDP bomb → patch + malformed-packet filtering.






## Slide 35 — UDP Flood: How It Works

A UDP flood sends a large volume of UDP packets to a target, aiming to exhaust bandwidth or processing capacity.

UDP does not require a handshake, so an attacker can generate traffic without first establishing TCP-style connections.

A flood may target random or specific ports. When packets arrive at ports with no listening service, the target may generate ICMP Port Unreachable messages, creating additional processing work.

Attackers may also spoof source IP addresses to hide the true origin.

Impact: Denial of service due to bandwidth or processing exhaustion.

Memory: UDP flood = too many UDP packets → bandwidth or CPU/resources exhausted.
## UDP Flood: Mitigation

The slide recommends:

- Rate-limit UDP traffic at the network perimeter.
    
- Close or filter UDP ports that do not need to be exposed.
    
- Disable or rate-limit ICMP Port Unreachable generation where appropriate, reducing the extra processing burden.
    
- Use dedicated DDoS protection for large-scale floods.
    

The slide also mentions DNS, NTP, and memcached amplification as examples of large UDP-based DDoS patterns. In amplification attacks, an attacker exploits a service that sends a much larger response than the request, often using a spoofed victim source address.

That can turn a relatively small amount of attacker traffic into a much larger flood directed at the victim.

Important distinction: UDP flood is the broad high-volume attack; amplification is one way attackers can make the resulting traffic much larger.





## Bringing It Together

This slide introduces the final summary: identify the recurring weaknesses shared by these attacks, then select defenses that address those weaknesses.

The central idea is that no single mitigation solves every transport-layer attack. You need to understand what each attack abuses.



## Full Mitigation Comparison

This is an important revision table from the lecture. Learn the primary mitigation for each attack.

|Attack|Primary mitigation in the lecture|
|---|---|
|TCP port scan|Minimise exposed services; scan detection; rate-limiting|
|TCP host sweep|Minimise exposed services; scan detection; segmentation|
|OS fingerprinting|Patching; stack normalization; defense in depth|
|TCP hijacking|TLS; randomized sequence numbers; anomaly monitoring|
|TCP RST injection|TLS; randomized sequence numbers; reset-rate monitoring|
|TCP SYN flood|SYN cookies; backlog tuning; SYN filtering; DDoS protection|
|UDP port scan|ICMP Unreachable rate-limiting; scan detection|
|UDP host sweep|ICMP Unreachable rate-limiting; segmentation|
|UDP bomb|Patching; firewall/IPS packet validation|
|UDP flood|Rate-limiting; close unnecessary ports; DDoS protection|

Exam technique: If a question asks for a mitigation, first identify the attack's target. For example, a full half-open backlog points directly to SYN cookies; malformed UDP fields point to patching and packet validation.

## Recurring Vulnerability Themes

The lecture identifies three important themes.

1. TCP has connection state, but not built-in cryptographic authentication of each packet.

TCP tracks sequence numbers and connection state. If an attacker can forge packets that pass TCP's checks, hijacking or RST injection may be possible.

2. UDP is lightweight, but has less built-in verification and state.

UDP does not establish a connection before sending a datagram. This makes it efficient, but it also means UDP itself does not verify the sender's identity before an application processes a packet.

3. Source IP spoofing appears in many attacks.

Source IP spoofing means falsifying the source address in a packet. Layer 4 does not independently prove that the source IP is genuine; source-address filtering and application-level authentication help address this problem.

Remember: TCP weakness = unauthenticated connection state. UDP weakness = minimal built-in state and verification.





## Layering Mitigations for High Security at Low Cost

The lecture emphasizes that effective security does not always require expensive products.

A sensible order is:

1. Free configuration changes: disable unnecessary services, enable SYN cookies, and filter spoofed source addresses at network boundaries.
    
2. Existing security equipment: configure firewall rate-limiting and IDS/IPS detection.
    
3. Specialist protection: use dedicated DDoS protection or deep packet inspection when the risk justifies the expense.
    

This is a risk-based approach. You should spend money in proportion to the value of the system and the potential impact of an attack.

Exam memory: Start with low-cost, high-value controls, then add more expensive controls when needed.