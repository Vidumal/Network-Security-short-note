

Firewall = control traffic; IDS = detect; IPS = detect and prevent.
Firewall = Rules → Check → Allow or Deny.


|Feature|Firewall|IDS|IPS|
|---|---|---|---|
|Main purpose|Prevent unauthorised traffic|Detect suspicious activity|Detect and prevent attacks|
|Main decision basis|Configured traffic rules|Signatures or anomalies|Signatures or anomalies|
|Alerts on attacks|Depends on implementation|Yes|Typically yes|
|Blocks traffic|Yes, according to policy|No|Yes|
|Typical placement|Network boundary or host|Often monitors a traffic copy or host activity|Inline with traffic|

Hardware firewall
- A dedicated physical appliance.
    
- Usually protects an entire network or network segment.
    
- Centralises security rules.
    
- Can offer high throughput.
    
- Higher initial cost.
    
- May not inspect traffic moving entirely within its network.


Software firewall
- Runs on an individual computer, server or virtual machine.
    
- Provides per-host control.
    
- Often inexpensive or built into the operating system.
    
- Uses the host's CPU and memory.
    
- May be disabled or bypassed if the host is compromised.

Hardware = protects the network; software = protects the host.



|Period|Development|Main improvement|
|---|---|---|
|Late 1980s|Packet filtering|Checks packet headers|
|1994|Stateful inspection|Tracks active connections|
|Mid-to-late 1990s|Circuit-level gateways and application proxies|Examines session or application-level communication|
|2000s|Unified Threat Management (UTM)|Combines several security functions in one appliance|
|Roughly 2007–2010 onward|Next-Generation Firewalls (NGFW)|Adds application awareness, deep inspection and integrated intrusion prevention|
|Parallel development|Web Application Firewall (WAF)|Specialises in protecting web applications|
The historical sequence teaches an important principle:

- Basic packet filters knew the packet's addresses and ports.
    
- Stateful firewalls added connection context.
    
- Proxy firewalls could examine communication at higher levels.
    
- Modern integrated firewalls combined multiple inspection and protection capabilities.
    

Exam memory trick: Headers → Connections → Sessions/Applications → Combined protection.


Note that the WAF developed as a web-specific specialisation alongside broader firewall developments; it is not simply the next universal replacement for every other firewall.



# Stateless vs stateful inspection


## Stateless firewall

Checks this packet against the rules

Each packet is judged independently. The firewall does not remember previous packets or the connection they belonged to.

## Stateful firewall

Checks the packet and connection state

Maintains a state table to track active connections. Return traffic belonging to a legitimate established connection can be permitted automatically.

| Stateless                                                                                       | Stateful                                                         |
| ----------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| No connection memory                                                                            | Maintains a connection state table                               |
| Checks individual packet headers                                                                | Checks packets in connection context                             |
| Usually faster and simpler                                                                      | Requires more memory and processing                              |
| Cannot inherently distinguish legitimate replies from unsolicited packets with matching headers | Can reject packets that do not fit the expected connection state |
Attack connection: A stateful firewall can help defend against some spoofed or out-of-state packets. However, it is not immune to every attack: for example, a SYN flood can exhaust its state table by creating many half-open connections.


Exam memory trick: Stateless = no memory; stateful = remembers the conversation.





# The seven firewall types

### Seven types at a glance

|Type|OSI layer|Main thing it checks|
|---|---|---|
|1. Packet Filtering|L3/L4|Packet headers|
|2. Stateful Inspection|L3/L4|Headers plus connection state|
|3. Circuit-Level Gateway|L5|Session establishment|
|4. Application-Layer / Proxy|L7|Application protocol and content|
|5. NGFW|L3–L7|Applications, users and deeper traffic details|
|6. WAF|L7|Web application requests and attack patterns|
|7. UTM|L3–L7|Multiple security functions in one appliance|

Quick OSI reminder:

- L3 — Network: IP addresses.
    
- L4 — Transport: TCP/UDP ports and transport information.
    
- L5 — Session: Establishing and managing sessions.
    
- L7 — Application: Protocols and application data, such as HTTP.
    

Memory trick: Packet → State → Circuit → Proxy → NGFW → WAF → UTM.







# Type 1: Packet Filtering Firewall — Mechanism


A packet filtering firewall examines the packet header, including:

- Source IP address.
    
- Destination IP address.
    
- Source port.
    
- Destination port.
    
- Protocol, such as TCP, UDP or ICMP.
    

It compares these details against an Access Control List (ACL).

An ACL is a list of rules. The lecture describes the rules as being processed from top to bottom, stopping at the first matching rule.

Example:

|Rule|Condition|Action|
|---|---|---|
|1|TCP destination port 443|Allow|
|2|TCP destination port 23|Deny|
|3|Other traffic|Follow the remaining policy|

The exact outcome depends on the rules and their order.

Important: This firewall is stateless. It examines packets independently and does not inspect their payload content.

### Packet Filtering: Advantages and disadvantages

|Advantages|Disadvantages|
|---|---|
|Very fast|Does not track connection state|
|Low processing overhead|Cannot inspect application payloads|
|Simple for basic policies|Large rule sets can be difficult to manage|
|Inexpensive|Fragmented packets can evade poorly designed filtering|
### Packet Filtering: Use cases

Use this type when you need:

- Router-based ACLs.
    
- Basic IP address, protocol and port filtering.
    
- A fast first line of perimeter defence.
    
- Simple protection for small networks or home routers.
    

Scenario: A router must block incoming Telnet traffic while allowing HTTPS. Basic packet filtering is a suitable option if header-based filtering meets the requirement.









# Type 2: Stateful Inspection Firewall — Mechanism



A stateful firewall does everything needed for ordinary header-based filtering but also keeps a state table.

The table records information about active connections, including addresses, ports, protocol and connection stage. The lecture also highlights TCP sequence and flag information.

How it works:

1. A client initiates a connection.
    
2. The firewall checks the connection attempt against its rules.
    
3. It records the legitimate connection's state.
    
4. Return traffic that matches the established connection can be permitted automatically.
    
5. Packets that do not fit the expected connection state may be rejected.
    

Example: Your PC connects to a website. The stateful firewall records the connection and recognises the website's reply as part of that connection.
Compare this with packet filtering: a basic stateless firewall has no connection memory.

### Stateful Inspection: Advantages and disadvantages

| Advantages                                                 | Disadvantages                                         |
| ---------------------------------------------------------- | ----------------------------------------------------- |
| More context-aware than stateless filtering                | Requires memory and processing                        |
| Simpler rules for return traffic                           | State table can be exhausted by a SYN flood           |
| Better resistance to some spoofing and scanning techniques | Generally cannot inspect encrypted payloads by itself |
| Can identify out-of-state packets                          | More complex to troubleshoot                          |
#### Attack to remember: SYN flood

A TCP connection commonly starts with a three-way handshake:

1. Client sends `SYN`.
    
2. Server replies `SYN-ACK`.
    
3. Client sends `ACK`.
    

In a SYN flood, an attacker sends many connection requests but does not complete the handshakes. The server or stateful firewall may retain numerous half-open connection entries, consuming resources.

Memory trick: SYN flood = requests arrive, handshakes remain unfinished, connection resources fill up.

This is a possible weakness, not a guarantee that every SYN flood will exhaust every stateful firewall.

### Slide 18 — Stateful Inspection: Use cases

Use it for:

- Standard enterprise perimeter firewalls.
    
- Networks that need connection-aware filtering.
    
- Environments that need stronger context than basic packet filtering without requiring full application-layer inspection.
    

Scenario: A business needs to allow employees to browse the internet and allow replies to those connections, while blocking unsolicited inbound connections. Stateful inspection is a good fit.


# Type 3: Circuit-Level Gateway — Mechanism

A circuit-level gateway operates at OSI Layer 5 (Session).

Its main purpose is to check whether a session is established correctly. For TCP, it can check the handshake:

`SYN → SYN-ACK → ACK`

Once the session has been validated, it does not inspect the actual payload content.

Imagine a receptionist who checks that a visitor has completed the correct entry procedure but does not inspect everything inside the visitor's bag.

The lecture identifies SOCKS proxying as a classic example of this approach.

Key distinction: A circuit-level gateway validates the session, not the application data flowing through it.
### Circuit-Level Gateway: Advantages and disadvantages

|Advantages|Disadvantages|
|---|---|
|Lower overhead than full application inspection|No content-level filtering after session validation|
|Hides internal network addressing from outside parties|Malicious payloads may pass through a valid session|
|Relatively fast after validation|Limited standalone use today|

Exam trap: A correctly established TCP session does not mean all the data inside that session is safe.

### Slide 21 — Circuit-Level Gateway: Use cases

The lecture lists:

- SOCKS-based application proxying.
    
- Hiding internal network topology.
    
- Situations where lightweight session validation is needed.
    
- Historical proxy-firewall deployments.
    

Today, circuit-level gateway concepts are often incorporated into broader systems rather than used as the only firewall.

Memory trick: Circuit gateway = checks the connection ceremony, not the conversation's content.






# Type 4: Application-Layer (Proxy) Firewall — Mechanism


The process is:

1. The client connects to the proxy.
    
2. The proxy terminates the client's connection.
    
3. The proxy inspects the application request using protocol-specific knowledge.
    
4. It opens a separate connection to the destination on the client's behalf.
    
5. It forwards permitted traffic and may sanitise content.
    

For example, an HTTP proxy can understand HTTP methods and requests. An FTP proxy can understand FTP commands.

Unlike a circuit-level gateway, an application proxy can inspect application-level content in detail.

Memory trick: Circuit gateway checks the session; application proxy checks what the application is doing.

### Application-Layer Proxy: Advantages and disadvantages

|Advantages|Disadvantages|
|---|---|
|Deep inspection and detailed content control|Higher processing overhead|
|Can hide the client's real address from the destination|May need separate protocol-specific proxy support|
|Can sanitise malicious content|Can become a bottleneck or single point of failure|

Important concept — anonymisation: The destination sees the proxy as the connecting party rather than directly seeing the original client connection. This does not guarantee complete anonymity.

Exam question: Why is a proxy firewall more resource-intensive than packet filtering?

Answer: It terminates and establishes separate connections and examines application protocols and content rather than checking only packet headers.
### Application-Layer Proxy: Use cases

Use it for:

- Corporate web and email proxies.
    
- Strict content filtering.
    
- Hiding internal client identities from external destinations.
    
- Environments that require detailed application-protocol control.
    

The lecture also identifies the proxy firewall's deep-inspection approach as a conceptual foundation for modern WAFs.







# Type 5: Next-Generation Firewall (NGFW) — Mechanism

An NGFW combines several capabilities:

- Traditional stateful inspection.
    
- Deep Packet Inspection (DPI).
    
- Application awareness.
    
- Integrated intrusion prevention.
    
- Often, user-identity-based security policies.
    
- In suitable configurations, inspection of decrypted SSL/TLS traffic.
    

What is application awareness?

An ordinary port-based rule might allow traffic because it uses TCP port 443. An application-aware firewall can try to identify the application generating that traffic rather than relying on the port alone.

This matters because different applications can use the same port.

The lecture also distinguishes NGFW from UTM: NGFW is built around a tightly integrated inspection architecture, whereas UTM commonly bundles separate security modules into one appliance.
### NGFW: Advantages and disadvantages

|Advantages|Disadvantages|
|---|---|
|Combines multiple security capabilities|More expensive|
|Provides application-level visibility|More complex to configure and manage|
|Can inspect SSL/TLS traffic when configured to decrypt it|Decryption and deep inspection can reduce throughput|
|Suits sophisticated, multi-vector threats|Many security functions depend on one central platform|

What is DPI?

Deep Packet Inspection examines more than basic packet headers. Depending on the product and configuration, it can analyse packet payloads and application-layer information.

What about encrypted traffic? An NGFW cannot automatically read all encrypted content. Inspection of HTTPS content generally requires an appropriate TLS inspection/decryption configuration and must account for certificate trust, privacy and application compatibility.### NGFW: Use cases

Use an NGFW when:

- Protecting a modern enterprise network.
    
- Applying policies based on applications rather than ports alone.
    
- Integrating intrusion prevention with firewall controls.
    
- Inspecting encrypted traffic where required and permitted.
    
- Consolidating several closely integrated security functions.
    

Scenario: A company wants to block a risky application even when it uses TCP port 443. An NGFW with suitable application identification is a better fit than a basic packet-filtering firewall.





# Type 6: Web Application Firewall (WAF) — Mechanism



A WAF specialises in HTTP and HTTPS traffic associated with web applications.

It sits in front of a web application and inspects requests and, depending on its configuration, responses for web-attack patterns.

The lecture specifically mentions:

- SQL injection (SQLi): Malicious input attempts to alter database queries.
    
- Cross-site scripting (XSS): Malicious script content is injected into a web page and executed in a victim's browser.
    
- Cross-site request forgery (CSRF): A victim's browser is induced to make an unwanted request to a site where the victim is authenticated.
    

A WAF can use two types of rule model:

1. Signature-based detection: Looks for known attack patterns.
    
2. Positive security model: Allows requests that match known-good patterns and denies requests that do not.
    

A WAF may use both.

Example: If an attacker sends a suspicious request to a public online banking website, a properly configured WAF may identify and block it before it reaches the application.

A WAF is not a complete replacement for secure coding. It can miss novel or obfuscated attacks, and its rules require maintenance.
### WAF: Advantages and disadvantages

|Advantages|Disadvantages|
|---|---|
|Specialised protection for web application attacks|Narrow scope: web applications|
|Can be deployed near the application or as a cloud service|Needs a broader firewall for general network protection|
|Can filter requests using web-attack patterns|Incorrect tuning can cause false positives or false negatives|

- False positive: Legitimate traffic is incorrectly blocked.
    
- False negative: Malicious traffic is incorrectly allowed.
    

Exam memory trick: WAF = Web Attacks Filter.
### WAF: Use cases

Use a WAF for:

- Public-facing e-commerce websites.
    
- Customer portals and online services.
    
- Cloud-hosted web applications.
    
- Web applications that need protection against common web-attack patterns.
    

Scenario: An online shop wants an additional layer of protection against SQL injection and XSS. A WAF is an appropriate choice, alongside secure application development and other network controls.






# Type 7: Unified Threat Management (UTM) — Mechanism
A UTM appliance combines multiple security functions in one physical or virtual appliance.

The lecture lists:

- Firewall.
    
- Antivirus.
    
- IDS/IPS.
    
- Content and URL filtering.
    
- VPN.
    
- Often, spam filtering.
    

Imagine a small business purchasing one security appliance instead of deploying and managing six separate security products.

UTM versus NGFW — the important difference

- UTM: Bundles multiple security functions, commonly as separate or relatively independent modules.
    
- NGFW: Integrates stateful inspection, application awareness, deep inspection and intrusion prevention into a tightly integrated inspection architecture.
    

Both can provide multiple protections. The distinction in this lecture is primarily their architectural approach, not simply the number of features.

### UTM: Advantages and disadvantages

|Advantages|Disadvantages|
|---|---|
|One management interface|Can become a performance bottleneck|
|Often cost-effective for smaller organisations|One appliance failure can affect many security functions|
|Easier initial deployment|May lack the depth of specialised standalone products|
|Fewer products and vendors to manage|Scaling can be more difficult|

Exam memory trick: UTM = Unified Threat Management — many security tools in one box.


### UTM: Use cases

Use UTM for:

- Small and medium-sized businesses.
    
- Branch offices.
    
- Organisations with limited dedicated security staff.
    
- Environments where straightforward management and cost efficiency matter.
    

Scenario: A small company needs firewall protection, antivirus, VPN and content filtering but does not have











## Why firewalls evolved 

### Slide 34 — Bringing it together

This is a section-divider slide. The purpose is to connect the seven firewall types and understand why each generation developed.

### Slide 35 — The evolution of firewalls, revisited

This slide is particularly useful for exam questions asking you to explain why a firewall type was developed.

1

Packet filtering — inspect headers

Early networks needed basic access control, and processing resources were limited. Simple header matching was fast and practical.

2

Stateful inspection — track connections

Stateless filtering lacked connection context. Stateful inspection improved security by recognising legitimate established connections and their return traffic.

3

Circuit gateway and proxy — inspect further

Connection tracking alone could not inspect application payloads. Circuit gateways validated sessions; application proxies examined protocol content in greater detail.

4

NGFW — integrate deeper inspection

Modern networks needed application awareness, intrusion prevention and better visibility into encrypted traffic. NGFW integrated these capabilities.

5

WAF and UTM — specialise or consolidate

WAF focuses deeply on web application threats. UTM combines multiple security functions to simplify management.

The overall progression is:

Header → Connection → Session → Application → Integrated protection.

The key trade-off is that deeper inspection usually provides more context and more precise security decisions, but it can require more processing, memory, configuration and cost.

Remember: WAF and UTM are different approaches, not necessarily later replacements for NGFW.








# Full summary comparison

Memorise the main use case for each firewall type.

|Firewall|Best use case|
|---|---|
|Packet filtering|Basic router ACL and port filtering|
|Stateful inspection|Standard enterprise perimeter protection|
|Circuit-level gateway|Session validation and hiding internal addresses|
|Application proxy|Detailed content control and client-address masking|
|NGFW|Modern enterprise security with application awareness|
|WAF|Protection for public-facing web applications|
|UTM|All-in-one security for small businesses and branch offices|


### Summary

The lecture's central lessons are:

- A firewall enforces an allow/deny policy.
    
- IDS detects and alerts; IPS detects and blocks.
    
- Stateful inspection tracks connections, unlike stateless filtering.
    
- The seven types differ in inspection depth, scope, complexity and cost.
    
- WAF specialises in web application protection, while UTM bundles multiple security functions.
    
- Hardware and software firewalls can be used together for defence in depth.








## Final attack revision table

|Attack|What the attacker does|Main concept to remember|
|---|---|---|
|SYN flood|Leaves many TCP handshakes unfinished|Exhaust connection resources|
|IP spoofing|Forges the source IP address|Fake sender|
|TCP hijacking|Attempts to interfere with an existing session|Take over a conversation|
|TCP RST injection|Sends a forged reset packet|Force a connection to terminate|
|Port scanning|Probes ports for reachable services|Find open doors|
|Fragmented-packet evasion|Exploits inadequate fragment handling|Split the packet|
|SQL injection|Alters database query logic through input|Change the database command|
|XSS|Injects a script into a page|Execute malicious script in a browser|
|CSRF|Tricks an authenticated browser into sending a request|Abuse an existing login session|
