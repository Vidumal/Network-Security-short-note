
The DMZ and the Zone-Based Model

- DMZ: Where public-facing servers are placed.
    
- Zone-Based Firewall: How policies are organised between network zones.

DMZ stands for Demilitarised Zone.


It is a separate network segment between an untrusted network, such as the internet, and a trusted internal network.



Why not put the public web server directly inside the internal network? Because the server must accept connections from outside, which exposes it to potential attacks.

The DMZ reduces the consequences of a compromise by restricting access from the public-facing server to internal systems.

### Example

Suppose an attacker compromises the company's public web server.

Without proper segmentation, the attacker may be able to reach internal employee computers or databases.

With a properly configured DMZ, the attacker is restricted to the DMZ and cannot freely access the internal network.

Exam memory trick: DMZ = Public servers outside, private systems protected inside.

Important: A DMZ does not make a compromised server harmless. It contains the damage only when the firewall rules and segmentation are correctly configured.





## Zone-Based Firewall: A configuration model, not a new technology

A Zone-Based Firewall (ZBF) organises network interfaces into logical security zones.

For example:

- `INSIDE` — trusted employee network.
    
- `OUTSIDE` — internet.
    
- `DMZ` — public-facing servers.
    

Instead of creating and managing rules independently for every interface, you define policies for traffic moving between zones.




In a typical ZBF configuration:

- OUTSIDE → DMZ: allow only required public services.
    
- OUTSIDE → INSIDE: deny unsolicited access.
    
- DMZ → INSIDE: deny or tightly restrict.
    
- INSIDE → OUTSIDE: allow the required outbound traffic.
    

The lecture describes traffic between different zones as denied by default unless an explicit zone-pair policy allows it. Traffic within the same zone is typically allowed by default.

Exam memory trick: ZBF = group interfaces into zones, then control traffic between zones.


## How DMZ and ZBF fit together

This slide combines the previous concepts.

A common design has three zones:

|Traffic direction|Example policy|Reason|
|---|---|---|
|OUTSIDE → DMZ|Allow HTTPS to the public web server|Public website must be reachable|
|OUTSIDE → INSIDE|Deny unsolicited connections|Protect private systems|
|DMZ → INSIDE|Deny or allow only specific required connections|Contain a compromised public server|
|INSIDE → OUTSIDE|Permit required internet access|Allow employees to work|
|INSIDE → DMZ|Permit required administration or application access|Support legitimate internal use|

Policies are directional. Allowing INSIDE → OUTSIDE does not automatically mean that all OUTSIDE → INSIDE traffic is permitted.

Why use ZBF instead of manually managing many individual interface rules? As a network grows, zone-based policies make it easier to understand, audit and maintain which areas can communicate.

Exam question: Why is unrestricted DMZ-to-INSIDE traffic dangerous?

Answer: If a public-facing DMZ server is compromised, the attacker could use it as a stepping stone into the trusted internal network.




# The four firewall placement architectures


 Introduction to placement architectures
This section compares four designs based on where the router and firewall devices sit.

The four designs are:

1. One firewall behind the border router.
    
2. One firewall in front of the border router.
    
3. Two firewalls, with the router before both.
    
4. Two firewalls, with the router between them.
    

To answer exam questions, remember both the strengths and weaknesses of each design.




## Architecture 1: Single firewall behind the border router


The traffic path is:

Internet → Border Router → Firewall → Internal network / DMZ

The router is the first device receiving external traffic. The firewall then applies the main security policies before traffic reaches the protected networks.

Advantages

- Simple to deploy and manage.
    
- Lower equipment and maintenance costs.
    
- One main point for policy management.
    

Disadvantages

- The firewall is a single point of failure.
    
- DMZ and internal traffic share the same firewall device and its resources.
    
- The border router itself is not behind the firewall.
    

Scenario: A small organisation wants a low-cost, straightforward setup and accepts the limitations of a single firewall.

Memory trick: Architecture 1 = cheap and simple, but one firewall is a single point of failure.







## Architecture 2: Single firewall in front of the border router


The traffic path is:

Internet → Firewall → Border Router → Internal network

Here, the firewall inspects incoming traffic before it reaches the router.

Advantages

- Filters traffic before it reaches the router.
    
- Protects the router from traffic that the firewall blocks.
    
- Can be conceptually straightforward for a small network where the firewall also acts as the primary gateway.
    

Disadvantages

- Less common as a standard design.
    
- Routing-protocol and management traffic to the router must be handled carefully by firewall policy.
    
- Adds policy complexity around the router's own traffic.
    

Scenario: A small network prioritises filtering external traffic before it reaches the border router.

Memory trick: Architecture 2 = firewall first protects the router, but the design is less standardised.






## Architecture 2: Single firewall in front of the border router

The traffic path is:

Internet → Firewall → Border Router → Internal network

Here, the firewall inspects incoming traffic before it reaches the router.

Advantages

- Filters traffic before it reaches the router.
    
- Protects the router from traffic that the firewall blocks.
    
- Can be conceptually straightforward for a small network where the firewall also acts as the primary gateway.
    

Disadvantages

- Less common as a standard design.
    
- Routing-protocol and management traffic to the router must be handled carefully by firewall policy.
    
- Adds policy complexity around the router's own traffic.
    

Scenario: A small network prioritises filtering external traffic before it reaches the border router.

Memory trick: Architecture 2 = firewall first protects the router, but the design is less standardised.






## Architecture 3: Dual firewall, router before both (screened subnet)


The general path is:

Internet → Router → Firewall 1 → DMZ / perimeter segment → Firewall 2 → Internal network

The key feature is that two distinct firewalls provide separate security boundaries.

Advantages

- Defence in depth: an attacker must get past multiple security controls.
    
- The DMZ can be isolated between distinct security boundaries.
    
- Each firewall can enforce policies suited to its position.
    

Disadvantages

- Higher hardware, licensing and maintenance costs.
    
- More complex configuration.
    
- Inconsistent rules between the two firewalls can create gaps or connectivity problems.
    

Scenario: An organisation has sensitive internal systems and needs stronger separation between its public-facing services and private network.

Memory trick: Architecture 3 = two firewalls, stronger separation, higher cost.





## Architecture 4: Dual firewall, router between the two


The traffic path is:

Internet → Firewall 1 → Router → Firewall 2 → Internal network

The first firewall filters external traffic before it reaches the router. The second firewall provides another security boundary before the internal network.

Advantages

- Firewall 1 protects the router directly.
    
- The router can focus on routing and Network Address Translation (NAT).
    
- Firewall 2 provides another filtering layer.
    

Disadvantages

- If Firewall 1 is bypassed, the router sits between the first and second firewalls without another firewall immediately in front of it.
    
- The router becomes an important dependency and potential chokepoint.
    
- The design is less common and can be more complex to standardise.
    

Memory trick: Architecture 4 = firewall, router, firewall — but the router sits between the security boundaries.





## Comparison of the four architectures

|Architecture|Devices|Main advantage|Main weakness|
|---|---|---|---|
|1. Single, behind router|1 firewall|Simple and cheap|Single point of failure|
|2. Single, in front of router|1 firewall|Protects the router directly|Less standardised design|
|3. Router before both firewalls|2 firewalls|Strong defence in depth|Higher cost and complexity|
|4. Router between firewalls|2 firewalls|Filters traffic before router and internal network|Router remains a dependency between security devices|

### How to answer a scenario question

- If the question emphasises low cost and simplicity, consider Architecture 1.
    
- If it emphasises protecting the border router before traffic reaches it, consider Architecture 2.
    
- If it emphasises strong separation and defence in depth, consider Architecture 3.
    
- If it specifically requires the router between two firewalls, explain Architecture 4 and its associated risk.
    

Do not say that two firewalls automatically make a network secure. Correct configuration, independent failure modes and consistent policies matter as well.






# Access Control Lists (ACLs)


An Access Control List (ACL) is an ordered set of rules that determines whether packets should be permitted or denied.

ACLs are commonly configured on Cisco routers and Layer 3 switches.

They can control traffic based on criteria such as:

- Source IP address.
    
- Destination IP address.
    
- Protocol, such as TCP, UDP or ICMP.
    
- TCP/UDP port number.
    

The most important concept is that rule order matters.




## ACL fundamentals

There are three rules you must understand before writing any ACL.

### Rule 1: Process rules from top to bottom

Suppose the ACL is:

```
access-list 10 deny host 192.168.1.5
access-list 10 permit any
```

The first rule denies traffic from `192.168.1.5`. The second rule permits traffic from other source addresses.

### Rule 2: First matching rule wins

Consider this ACL:

```
access-list 10 permit any
access-list 10 deny host 192.168.1.5
```

The first rule matches every source address, including `192.168.1.5`. Therefore, the second rule is never reached for that host.

The host is permitted, even though you wrote a deny statement.

This is an ACL configuration mistake. Specific deny rules should be placed before broader permit rules when the intended policy requires that.

### Rule 3: Implicit deny

Every ACL has an invisible final rule:

```
deny any
```

For an Extended ACL, the equivalent idea is that all packets not matched by an explicit permit rule are denied.

For example:

```
access-list 101 deny tcp host 192.168.1.5 host 192.168.1.10 eq 23
```

This denies the specified Telnet traffic. But without any further permit statements, all other traffic that reaches this ACL is also denied by the implicit deny.

To allow other traffic, add an explicit permit rule where appropriate:

```
access-list 101 deny tcp host 192.168.1.5 host 192.168.1.10 eq 23
access-list 101 permit ip any any
```

Now the specified Telnet traffic is denied and all other IPv4 traffic is permitted by this ACL.

Warning: `permit ip any any` is broad. Use it only if allowing all remaining IPv4 traffic is actually the intended policy.

### Rule 4: Direction matters

An ACL can be applied:

- `in` — to packets entering an interface.
    
- `out` — to packets leaving an interface.
    

For example:

```
interface GigabitEthernet0/1
 ip access-group 101 in
```

This applies ACL 101 to traffic entering that interface.

Exam memory trick: ACL = Order, First match, Implicit deny, Direction.




# Standard ACLs


A Standard ACL filters traffic based only on the source IP address.

It cannot distinguish between different destination addresses, protocols or ports.

Traditional Cisco numbered Standard ACL ranges are:

- `1–99`
    
- `1300–1999`
    

### Example

```
access-list 10 deny host 192.168.10.5
access-list 10 permit any
```

This blocks source `192.168.10.5` and permits other source addresses, regardless of the destination.

Imagine that this host needs access to Server A but must be blocked from Server B. A Standard ACL cannot express that distinction by itself because it checks only the source address.

### Advantages

- Simple to write.
    
- Easy to understand.
    
- Useful when the source address alone determines the policy.
    

### Disadvantages

- Cannot match a specific destination, port or protocol.
    
- May block legitimate traffic if placed too far from the destination.
    

Exam memory trick: Standard ACL = Source only.





# Extended ACLs


An Extended ACL can filter using:

- Source IP address.
    
- Destination IP address.
    
- Protocol.
    
- Source and destination ports, where supported.
    

Traditional Cisco numbered Extended ACL ranges are:

- `100–199`
    
- `2000–2699`
    

### Example

Suppose:

- Guest subnet: `192.168.10.0/24`
    
- Protected server: `192.168.20.5`
    
- Telnet port: TCP 23
    

The requirement is to block Telnet from the guest subnet to that server, while permitting other IPv4 traffic.

```
access-list 101 deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.5 eq 23
access-list 101 permit ip any any
```

The first line matches the specific combination of source network, destination host, TCP protocol and destination port 23. The second line permits everything else that reaches this ACL.

Unlike a Standard ACL, the Extended ACL can distinguish between the same guest computer connecting to different servers or services.

### Advantages

- Precise traffic filtering.
    
- Can block a specific service without blocking all communication from the source.
    
- Suitable for enforcing detailed network security policies.
    

### Disadvantages

- More complex to write and troubleshoot.
    
- Poor rule ordering can undermine the intended policy.
    
- Incorrect rules can interrupt legitimate services.
    

Exam memory trick: Extended ACL = Source + Destination + Protocol + Port.




# Named ACLs



A Named ACL uses a descriptive name instead of a number.

For example:

```
ip access-list extended BLOCK_TELNET_TO_SERVER
 deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.5 eq 23
 permit ip any any
```

The name `BLOCK_TELNET_TO_SERVER` makes the policy's purpose clear.

A Named ACL can be either Standard or Extended. It does not introduce a new filtering capability.

### Why use Named ACLs?

- Easier to understand in large configurations.
    
- Easier to maintain.
    
- Individual entries can be added, removed or reordered using sequence numbers on supported Cisco IOS versions.
    

Exam memory trick: Named ACL = Same filtering logic, clearer name.




## Standard ACL command format


The general syntax is:

```
access-list <number> <permit|deny> <source> [wildcard-mask] [log]
```

Example:

```
access-list 10 permit 192.168.100.0 0.0.0.31
```

This permits source addresses in the range represented by `192.168.100.0` with wildcard mask `0.0.0.31`.

Another example:

```
access-list 10 permit host 192.168.100.5
```

This matches one source host.

### What is a wildcard mask?

A wildcard mask is the inverse of a subnet mask.

- `0` means the corresponding bit must match.
    
- `1` means the corresponding bit may vary.
    

For example, the `/24` network `192.168.10.0/24` has a subnet mask of `255.255.255.0` and a wildcard mask of `0.0.0.255`.

A Standard ACL can be applied to an interface like this:

```
interface GigabitEthernet0/0
 ip access-group 10 out
```

The `out` direction means that the ACL filters traffic leaving that interface.

Exam memory trick: Wildcard = 0 must match; 1 can vary.






## Extended ACL command format

The general form is:

```
access-list <number> <permit|deny> <protocol> <source> <source-wildcard> <destination> <destination-wildcard> [operator port]
```

The full syntax has optional parts, including source ports, destination ports, `established` and `log`.

### Example from the lecture

```
access-list 101 deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.5 eq 23
```

Read it from left to right:

- `101` — Extended ACL number.
    
- `deny` — block matching traffic.
    
- `tcp` — match TCP traffic.
    
- `192.168.10.0 0.0.0.255` — source subnet.
    
- `host 192.168.20.5` — destination server.
    
- `eq 23` — destination port equals 23, used by Telnet.
    

### What does `established` mean?

In Cisco Extended ACL syntax, `established` is a TCP-specific match that checks whether the ACK or RST flag is set. It is sometimes used as a basic way to permit certain return TCP traffic.

It is not a full stateful inspection mechanism and should not be treated as equivalent to a stateful firewall.




## Named ACL command format

A Named Extended ACL is configured in its own sub-mode:

```
ip access-list extended BLOCK_TELNET_TO_SERVER
 10 deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.5 eq 23
 20 permit ip any any
```

Then apply it:

```
interface GigabitEthernet0/1
 ip access-group BLOCK_TELNET_TO_SERVER in
```

The sequence numbers `10` and `20` identify the entries and make it easier to insert or reorder rules.

Remember:

- `standard` or `extended` specifies the filtering type.
    
- The name identifies the list.
    
- The entries inside the list define the actual permit/deny logic.



## ACL placement rules

This is one of the most important exam slides.

Standard ACL — close to the destination

It can filter only by source IP. Applying it too early might block that source from reaching other destinations that should remain accessible.

Extended ACL — close to the source

It can match the source, destination, protocol and port, so unwanted traffic can be stopped early without unnecessarily blocking other destinations.

### Example

A source computer must be blocked from reaching one particular server but must retain access to other servers.

- A Standard ACL cannot distinguish the destination.
    
- An Extended ACL can distinguish the destination and service.
    
- Therefore, the Extended ACL can usually be applied near the source.
    

Exam memory trick: Standard = destination; Extended = source.

These are Cisco design best practices rather than absolute restrictions. The final placement must also account for topology, traffic direction and other security requirements.



## Worked example: Extended ACL

The requirement is:

> Block Telnet from `192.168.10.0/24` to server `192.168.20.5`, while permitting everything else.

Configuration:

```
access-list 101 deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.5 eq 23
access-list 101 permit ip any any

interface GigabitEthernet0/1
 ip access-group 101 in
```

The ACL is applied inbound on the gateway interface facing the source subnet, assuming that is how the network is connected.

### What happens to each packet?

|Traffic|Result|
|---|---|
|Guest subnet → `192.168.20.5`, TCP port 23|Denied|
|Guest subnet → `192.168.20.5`, TCP port 443|Permitted|
|Guest subnet → another destination, other IPv4 traffic|Permitted|

The second ACL entry is essential because of the implicit deny.

Important exam detail: This example permits all remaining IPv4 traffic. If the security requirement is stricter, the permit rules must be narrowed to the required services.



## Worked example: Named ACL

This achieves the same policy as Slide 23, but the ACL has a descriptive name.

```
ip access-list extended BLOCK_TELNET_TO_SERVER
 deny tcp 192.168.10.0 0.0.0.255 host 192.168.20.5 eq 23
 permit ip any any

interface GigabitEthernet0/1
 ip access-group BLOCK_TELNET_TO_SERVER in
```

The filtering behaviour is equivalent to the numbered Extended ACL when the rules and attachment point are the same.

Exam question: Does using a Named ACL make filtering more powerful?

Answer: No. It improves readability and maintainability, but the filtering capabilities come from whether the ACL is Standard or Extended.

## Slide 25 — ACL summary

Memorise this table.

| Feature                | Standard         | Extended           | Named                      |
| ---------------------- | ---------------- | ------------------ | -------------------------- |
| Matches source IP      | Yes              | Yes                | Depends on underlying type |
| Matches destination IP | No               | Yes                | Depends on underlying type |
| Matches protocol/ports | No               | Yes                | Depends on underlying type |
| Numbered ranges        | 1–99, 1300–1999  | 100–199, 2000–2699 | Uses a name                |
| Recommended placement  | Near destination | Near source        | Same as underlying type    |