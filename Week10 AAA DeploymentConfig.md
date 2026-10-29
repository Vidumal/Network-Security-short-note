
Local AAA means every router or switch has its own user database.


Advantages

- No external AAA server is required.
    
- It can continue authenticating local users when the external network is unavailable.
    
- It is straightforward to configure for a small network.
    

Disadvantages

- User accounts must be managed on every device.
    
- Different devices can end up with inconsistent accounts.
    
- Logs are separate unless additional central logging is configured.


Example: You change an administrator's password on Router 1 but forget Router 2. The account information is now inconsistent.

Memory trick: Local = each device looks after itself.


## Server-based (centralised) AAA

With centralised AAA, routers and switches forward requests to a dedicated AAA server. The server checks the request and returns a decision.

Examples:

- A company uses a central TACACS+ server for its network administrators.
    
- A university uses a RADIUS server to authenticate students connecting to Wi-Fi.
    

Advantages

- One central user database.
    
- Consistent account management.
    
- Centralised accounting and auditing.
    
- Easier administration of large networks.
    

Disadvantages

- Authentication through the server can fail if the server becomes unreachable and no working fallback is available.
    
- More infrastructure and configuration are required.
    
- The server becomes a high-value target.



### Comparison

|Feature|Local AAA|Centralised AAA|
|---|---|---|
|User database|On each device|Central server|
|Scalability|Poor for large networks|Good for large networks|
|External dependency|None for local authentication|Depends on server availability|
|Consistency|Must be managed per device|Centrally managed|
|Typical use|Small networks or fallback|Enterprise networks|

Exam question: A university has hundreds of access points and wants one consistent user database. What should it use?

Answer: Centralised AAA.



## Device administration vs. network access

### Introduction to the two types of access

The key question is: What are we protecting?

There are two different goals:

- Protecting the network devices themselves.
    
- Controlling who can connect to the network.


## Device administration AAA



This controls who can log in to a router or switch and which commands they can execute.

Example: An administrator connects to a Cisco router through SSH.

AAA can check:

1. Is the administrator's identity valid?
    
2. Can the administrator enter privileged mode?
    
3. Is the administrator allowed to execute a configuration command?
    
4. Should the session and commands be recorded?
    

The important concept is fine-grained, command-level authorization.

Traditional protocol choice: TACACS+.


Memory trick: Device administration = protect the router and its commands.




### Network access AAA

This controls whether a user or device can connect to a network.

Examples:

- A student connecting to university Wi-Fi.
    
- An employee connecting through a VPN.
    
- A laptop authenticating to a wired port using 802.1X.
    

The question is: Can this user or device get network access?

Traditional protocol choice: RADIUS.

Memory trick: Network access = control entry to the network.



TACACS+ is traditionally suited to device administration because it separates AAA functions and supports detailed command authorization.

RADIUS is traditionally suited to network access because it is lightweight and designed for high volumes of simultaneous connections.

Neither protocol is automatically the best choice for every situation. Choose based on the requirements.




### RADIUS: mechanism and use cases

RADIUS stands for Remote Authentication Dial-In User Service.

Important facts from the lecture:

- Transport: UDP
    
- Authentication/authorization port: 1812
    
- Accounting port: 1813
    
- Encryption: password field only in the RADIUS packet design described by the lecture
    
- Authentication and authorization: combined in a single response
    

Common uses include Wi-Fi authentication, VPN remote access, and other high-volume network-access scenarios.

Example: When you connect to university Wi-Fi, the access point can forward your credentials to a RADIUS server for verification.

Memory trick: RADIUS = fast, lightweight network entry.
### RADIUS: strengths and weaknesses

Strengths

- Open, multi-vendor standard.
    
- Supported by equipment from many manufacturers.
    
- Lightweight and suitable for high-volume authentication.
    
- Useful in mixed-vendor networks.
    

Weaknesses

- The username and other attributes are not encrypted by RADIUS itself.
    
- UDP does not itself guarantee delivery.
    
- Authentication and authorization are less independently controlled than in TACACS+.
    

Note: UDP can still be used in applications that implement their own retry mechanisms. The lecture's comparison is about the transport protocol's built-in behaviour.






### TACACS+: mechanism and use cases

TACACS+ stands for Terminal Access Controller Access-Control System Plus.

Important facts:

- Transport: TCP
    
- Port: 49
    
- Encryption: packet body, rather than only the password field
    
- AAA functions: handled separately
    
- Common use: router and switch administration, including command-level authorization
    

Example: A junior technician can be permitted to run diagnostic commands while being prevented from changing the router configuration.

Memory trick: TACACS+ = detailed control of technical administrators.



### TACACS+: strengths and weaknesses

Strengths

- Greater packet-body confidentiality than the RADIUS design described in the lecture.
    
- Independent AAA functions allow more granular control.
    
- Supports command-level authorization.
    
- TCP provides reliable transport delivery.
    

Weaknesses

- The lecture describes it as Cisco-proprietary.
    
- TCP and its additional protocol functionality create more overhead than the lightweight RADIUS design.



### Full comparison

This table is one of the most important things to memorise for the exam.

|Feature|RADIUS|TACACS+|
|---|---|---|
|Transport|UDP|TCP|
|Ports|1812 authentication; 1813 accounting|49|
|Encryption|Password field|Entire packet body|
|AAA separation|Authentication and authorization combined|AAA functions separated|
|Vendor support|Open, multi-vendor|Cisco-proprietary in this lecture|
|Overhead|Lower|Higher|
|Typical use|Wi-Fi, VPN, network access|Router/switch administration|

Fast memory rule:

- RADIUS → UDP, 1812/1813, network access, lightweight.
    
- TACACS+ → TCP, 49, command control, packet-body encryption, separate AAA functions.
    

Common exam traps:

- TACACS+ uses TCP port 49, not UDP port 49.
    
- RADIUS does not encrypt the entire packet by itself.
    
- TACACS+ is the traditional choice for command-level authorization.

### Full comparison

This table is one of the most important things to memorise for the exam.

|Feature|RADIUS|TACACS+|
|---|---|---|
|Transport|UDP|TCP|
|Ports|1812 authentication; 1813 accounting|49|
|Encryption|Password field|Entire packet body|
|AAA separation|Authentication and authorization combined|AAA functions separated|
|Vendor support|Open, multi-vendor|Cisco-proprietary in this lecture|
|Overhead|Lower|Higher|
|Typical use|Wi-Fi, VPN, network access|Router/switch administration|

Fast memory rule:

- RADIUS → UDP, 1812/1813, network access, lightweight.
    
- TACACS+ → TCP, 49, command control, packet-body encryption, separate AAA functions.





## AAA method lists and fallback


### What is a method list?

A method list is an ordered list of authentication methods that a device tries.

Example:

1. Try the central TACACS+ server.
    
2. If the server is unavailable, try the local user database.
    

This prevents a central server outage from automatically locking administrators out, provided a valid local account and fallback configuration exist.

Memory trick: Method list = first choice, backup choice.



### Method lists work like ACL order

Remember the ACL lecture: rules are processed in order.

AAA method lists also use an order, but there is an important distinction about when the device moves to the next method.

Consider:
```
aaa authentication login default group tacacs+ local
```

This means:

1. Try TACACS+ first.
    
2. Try local authentication if TACACS+ is unavailable.

Two possible outcomes:

- TACACS+ does not respond: The device can fall back to local authentication.
    
- TACACS+ responds and rejects the password: This is a definitive denial, not simply an unavailable server, so the device should not bypass it by automatically trying the local database.
    

Exam memory rule: No response can trigger fallback; a denial is not the same as no response.

## Cisco AAA configuration

These slides introduce the actual configuration commands. Focus on what each command does and the order of configuration. The examples below are lecture templates; do not reuse their sample passwords or shared keys in a real network.

### Step 1: Enable AAA

```
aaa new-model
```

This enables the AAA framework on the Cisco device.

Remember: Enable AAA before configuring the AAA methods.


### Step 2: Configure local AAA

```
username admin privilege 15 secret StrongPass123
aaa authentication login default local
```

Explanation:

- `username admin` — creates a local user called `admin`.
    
- `privilege 15` — assigns privilege level 15, the highest level in the lecture's Cisco IOS scale.
    
- `secret ...` — configures the user's secret credential.
    
- `aaa authentication login default local` — uses the local user database for the default login authentication method list.
    

Exam memory: `local` = use the device's own user database.


### Step 3: Configure TACACS+

```
tacacs server TACACS-SERVER1
 address ipv4 192.168.1.100
 key MySharedKey

aaa authentication login default group tacacs+ local
aaa authorization exec default group tacacs+ local
aaa accounting exec default start-stop group tacacs+
```

The shared key is a secret used between the device and server. It is not the administrator's login password.

Remember the three command keywords:

- Authentication = verify identity.
    
- Authorization = decide permissions.
    
- Accounting = record activity.





### Step 4: Configure RADIUS

```
radius server RADIUS-SERVER1
 address ipv4 192.168.1.200 auth-port 1812 acct-port 1813
 key MySharedKey

aaa authentication login default group radius local
```

Explanation:

- Defines the RADIUS server.
    
- Sets its IP address.
    
- Specifies authentication port 1812 and accounting port 1813.
    
- Configures the shared secret.
    
- Tries RADIUS first, then local authentication if RADIUS is unavailable.



Port memory trick: 1812 = authentication; 1813 = accounting.


### Understanding method-list order

Compare:

```
aaa authentication login default group tacacs+ local
```

```
aaa authentication login default group radius local
```

The first command tries TACACS+ before local authentication. The second tries RADIUS before local authentication.

The method order affects the security and availability of the system.

Practical warning: Misconfigured AAA can lock administrators out. Before changing a production device, verify the fallback account, test with a separate administrative session where possible, and follow your change-control process.





