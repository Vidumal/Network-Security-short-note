

Authentication
Who are you?
Example: You enter your username and password, and the system verifies your credentials.

Authorization
What are you allowed to do?
Example: You are allowed to view files but not delete them.

Accounting
What did you do?
Example: The system records that you logged in at 9:00 AM and ran a particular command.



AAA is a security framework used to manage access to systems, devices and resources.
Imagine an employee logging in to a company router:

- Authentication checks the employee's identity.
    
- Authorization determines which commands the employee can run.
    
- Accounting records the commands or activities performed.
    

Exam definition: AAA is a framework that combines identity verification, access permission decisions and activity recording.



The lecture covers:

1. The three AAA functions.
    
2. Authentication methods and factors.
    
3. Authorization, Cisco privilege levels and four access control models.
    
4. Accounting methods and their importance for auditing and investigation.


### Why three separate functions, not one?

Being authenticated does not mean you can do everything.

Suppose a junior IT technician logs in successfully to a router:

- Authentication: The technician's identity is verified.
    
- Authorization: The technician can run permitted `show` commands but cannot change critical configurations.
    
- Accounting: The system records the technician's activity.
    

Accounting also matters when access is denied. Repeated failed attempts may indicate someone is trying to guess a password.

The sequence is important: authorization needs an identity or other defined subject to evaluate, while accounting can record both permitted and denied events.

Exam trap: Do not say authentication and authorization are the same thing. A user can be authenticated but lack permission to access a particular resource.


|AAA function|Examples of choices|
|---|---|
|Authentication|Password, certificate, biometric, MFA|
|Authorization|DAC, MAC, RBAC, RuBAC|
|Accounting|Session logs, command logs, resource usage logs|

A system might use a password and authenticator app for authentication, role-based access control for authorization, and centralised logs for accounting.

The three functions work together, but each has a different job.

## Authentication: proving who you are

### Authentication section

Authentication verifies that an entity is who it claims to be.

The lecture introduces four broad groups of methods:

1. Something you know : This method checks a secret that the user should know.
		Examples: Password , PIN , Passphrase , Security-question answer
		Advantages : - Cheap and easy to implement , No special hardware is required ,
		A compromised password can be replaced.
		Weaknesses : - Password guessing , Phishing , Shoulder surfing , Keylogging ,Password reuse across multiple websites.
    
2. Something you have : This method verifies that you possess a particular physical or digital item.
		Examples: - Hardware security key , Smart card , Authentication token , Authenticator app generating a one-time code , SMS one-time code.
		Advantages: A password alone is not enough for an attacker to pass the additional possession check.
		Weaknesses: The item may be lost or stolen. SMS codes can be exposed through attacks such as SIM swapping.
    
3. Something you are : physical or behavioural biometrics 
	1. *physical* 
		Examples: Fingerprint , Facial geometry , Iris or retina pattern , Hand geometry.
		Advantages: Physical traits can be highly distinctive, Convenient for users.
	    Weaknesses: Specialised sensors may be required , Physical conditions can interfere with measurements , If biometric data is compromised, the trait cannot simply be replaced like a password.
	2. behavioural biometrics
		Examples: Typing rhythm , Walking pattern , Signature speed and rhythm , Speech cadence.
	    Advantages: Can support continuous authentication during a session , May detect suspicious changes after the initial login.
	    Weaknesses: Behaviour varies with stress, fatigue, injury and devices , Systems can incorrectly reject legitimate users or accept unusual behaviour , Methods are not equally mature or reliable.


			Difference to remember:

			- Physical biometrics = what your body is like.
    
			- Behavioural biometrics = how you act.



4. Multi-factor authentication, which combines different factor categories.

MFA combines at least two authentication factors from different categories.

Examples:

- Password + authenticator-app code.
    
- PIN + fingerprint.
    
- Password + hardware security key.
    

Two passwords do not count as two-factor authentication because both are in the “something you know” category.

Why is MFA useful? If an attacker steals a password, the additional factor can prevent the attacker from logging in.

However, MFA is not invulnerable. SMS-based MFA can be affected by SIM swapping, and attackers may trick users into approving unexpected push notifications through MFA prompt bombing.

Memory trick: MFA = two or more different kinds of proof.



### Authentication methods compared

|Method|Example|Main weakness|
|---|---|---|
|Something you know|Password|Can be guessed or phished|
|Something you have|Security key|Can be lost or stolen|
|Physical biometrics|Fingerprint|Cannot easily be reissued if compromised|
|Behavioural biometrics|Typing rhythm|Behaviour naturally changes|
|MFA|Password + authenticator app|Adds friction and can still be attacked|

Exam tip: When given an example, identify the factor category first, then explain one advantage and one limitation.



## Authorization: deciding what you can do

Authentication establishes identity. Authorization determines the actions and resources that identity is allowed to access.

For example, two employees may both log in successfully, but only the network administrator may change router configuration.

### Privilege levels and command authorization

Cisco IOS uses privilege levels from 0 to 15.

- Level 0: Very limited access.
    
- Level 1: Basic user EXEC access.
    
- Level 15: Full privileged administrative access.
    

Command authorization goes further than simply assigning a login level. It can control which individual commands a user may execute.

Example:

- Junior technician: permitted to run `show ip interface brief`.
    
- Network administrator: permitted to enter `configure terminal` and change configurations.
    

Why does this matter? If every authenticated user has administrative access, a compromised account can cause much greater damage.

### Access control models overview

Your lecture introduces four models:

- DAC: Discretionary Access Control.
    
- MAC: Mandatory Access Control.
    
- RBAC: Role-Based Access Control.
    
- RuBAC: Rule-Based Access Control.

All four determine who can access a resource, but they use different methods to make the decision.




### DAC: Discretionary Access Control

In DAC, the resource owner decides who can access the resource.

Example: You create a document and grant your colleague permission to read it.

Advantages:

- Flexible.
    
- Owners can manage permissions themselves.
    

Disadvantages:

- Different owners may make inconsistent decisions.
    
- Permissions can become difficult to manage across a large organisation.
    

Memory trick: DAC = Decides the owner.







### MAC: Mandatory Access Control

In MAC, a central authority sets security classifications and controls access according to those classifications.

Example classifications:

- Top Secret.
    
- Secret.
    
- Confidential.
    
- Unclassified.
    

A user's clearance is compared with the resource's classification, according to the system's security policy. Ordinary users cannot simply override the classification because they own the file.

Advantages:

- Consistent and centrally controlled.
    
- Appropriate for highly sensitive environments.
    

Disadvantages:

- Less flexible.
    
- Requires careful administration of labels and clearances.
    

Memory trick: MAC = Mandatory labels controlled centrally.






### RBAC: Role-Based Access Control

In RBAC, permissions are assigned to job roles, and users receive permissions through their assigned roles.

Example:

- HR Manager: access to personnel records.
    
- Sales Representative: access to the customer relationship management system.
    
- Network Administrator: access to network management tools.
    

If an employee changes jobs, the administrator can change the employee's role rather than individually changing every permission.

Advantages:

- Efficient for large organisations.
    
- Consistent permissions for people with the same role.
    

Disadvantages:

- Roles must be designed and maintained carefully.
    
- Poorly designed roles can grant excessive access.
    

Memory trick: RBAC = Role decides.






### RuBAC: Rule-Based Access Control

In RuBAC, access is determined by conditional rules evaluated when an access request occurs.

Example rules:

- Allow access only during business hours.
    
- Allow access only from an approved IP address.
    
- Block access from devices that fail a required security check.
    

A rule can depend on time, location or other conditions.

Advantages:

- Flexible and context-aware.
    
- Access decisions can change as conditions change.
    

Disadvantages:

- Complex rules can be difficult to manage.
    
- Conflicting or incorrectly ordered rules can cause unexpected results.
    

Your lecture connects RuBAC to ACLs: a firewall ACL evaluates traffic against rules to decide whether to permit or deny it.

Memory trick: RuBAC = Rules decide.




### Four access control models compared

|Model|Who or what decides?|Example|
|---|---|---|
|DAC|Resource owner|File owner grants read access|
|MAC|Central authority and classifications|Classified government system|
|RBAC|Job role|HR staff access personnel files|
|RuBAC|Conditional rules|Access allowed only during office hours|




## Accounting: recording what happened

### Accounting section

Accounting records activity so it can be reviewed later.

It is useful for:

- Security audits.
    
- Incident investigation.
    
- Compliance.
    
- Billing and usage tracking.
    
- Detecting unusual activity.
    

A user can be properly authenticated and authorised, yet their actions may still need to be recorded for accountability.


### Session accounting


Session accounting records when a session starts and stops.

Example:

- Login: 9:14 AM.
    
- Logout: 9:47 AM.
    
- Session duration: 33 minutes.
    

It can help answer:

- Who logged in?
    
- When did the session begin?
    
- When did it end?
    
- How long was the user connected?
    

Advantages: Simple and relatively low overhead.

Limitation: It does not tell you exactly what the user did during the session.

Memory trick: Session accounting = when and how long.



### Command accounting

Command accounting records individual commands executed by a user.

Example log:

- 9:15 AM — entered configuration mode.
    
- 9:16 AM — selected an interface.
    
- 9:17 AM — changed an interface setting.
    

It is especially useful for:

- Investigating incidents.
    
- Reconstructing administrative actions.
    
- Demonstrating which commands were recorded under a user's identity.
    
- Compliance and accountability.
    

The slide identifies TACACS+ command accounting as an example.

Advantages: Detailed activity records.

Limitations: Generates more logs and requires storage, monitoring and protection.

Memory trick: Command accounting = what commands.



### Resource accounting

Resource accounting records how much of a resource was consumed.

Examples:

- Data transferred.
    
- Bandwidth used.
    
- Connection time.
    
- VPN traffic volume.
    

Example: A corporate VPN gateway records that a remote account transferred 8 GB of data during a session.

This can help with billing, capacity planning and identifying unusual data transfers.

However, a large transfer could be a legitimate backup or possible data theft. Resource accounting alone cannot tell you which without additional context.

Memory trick: Resource accounting = how much.



### Accounting types compared

|Type|What it records|Best remembered as|
|---|---|---|
|Session|Login, logout and duration|When and how long|
|Command|Individual commands|What was done|
|Resource|Data volume, bandwidth or time consumed|How much was used|

These are different levels of detail. A good monitoring system may use more than one.



