# entra-pim-lab
Microsoft Entra PIM Lab Just-in-Time Access & Identity Governance
Overview
This lab demonstrates Privileged Identity Management (PIM) in Microsoft Entra ID. The goal was to eliminate standing privileged access by implementing a just-in-time access model where users must request, justify, and activate roles on demand. Roles are time-bound and auto-expire. Every activation is logged with a full audit trail.
This is a hands-on implementation of Zero Trust principles specifically the idea that no one should have persistent admin access. Access is granted only when needed, only for as long as needed, and only after verification.
<img width="975" height="570" alt="image" src="https://github.com/user-attachments/assets/2260e3e0-31e1-4226-b927-223f47450898" />

Tools Used
ToolPurposeMicrosoft Entra IDIdentity platform and PIM engineMicrosoft Entra ID P2Required license for PIM and Access ReviewsPrivileged Identity ManagementJust-in-time role activation and governanceMicrosoft SentinelSIEM for identity threat detection (Phase 4 in progress)

Architecture
The diagram below shows the full flow built in this lab.
The blue row is the access flow  a user requests a privileged role, must provide justification and pass MFA, an approver reviews the request, and the role activates for exactly one hour then auto-expires.
The pink row is the detection and response layer  every activation is captured in audit logs, Sentinel fires an alert on suspicious patterns, and a Logic App auto-revokes the role.
<img width="800" height="485" alt="image" src="https://github.com/user-attachments/assets/e3784f39-05ed-435d-94cc-acc173ad7ceb" />


What I Built
Phase 1 Enable PIM and Configure Role Settings
Enabled Privileged Identity Management on the Microsoft Entra ID tenant. Navigated to Identity Governance and initialized PIM.
Configured role settings for Security Reader:

Activation maximum duration: 1 hour
Require justification on activation: enabled
Require Azure MFA on activation: enabled
Require approval to activate: enabled with admin account as approver

These settings enforce the principle of least privilege. No user has standing access to any privileged role. Every activation requires conscious action, documentation, and verification.
<img width="975" height="570" alt="image" src="https://github.com/user-attachments/assets/2260e3e0-31e1-4226-b927-223f47450898" />


Phase 2
Create Eligible Assignment
Created a test user account with no default permissions. Assigned Security Reader as an eligible role — not active. Eligible means the user has the right to request the role but does not hold it until they activate it.
Assignment details:

Role: Security Reader
Member: test user
Assignment type: Eligible
Duration: time-bound, one year

<img width="975" height="661" alt="image" src="https://github.com/user-attachments/assets/0b744446-def3-48c0-9a52-101d71122897" />


Phase 3 
JIT Activation Flow
Signed in as the test user in a separate browser session. Navigated to PIM, found Security Reader under eligible assignments, and activated it.
Activation required:

A written justification explaining why the role was needed
MFA verification confirming the identity of the requester
Approval from the admin account

Once approved, the role became active for exactly one hour. After one hour it expired automatically with no manual action required.
<img width="975" height="578" alt="image" src="https://github.com/user-attachments/assets/a0fd066d-3219-4d82-92be-9c5e027ffad1" />


Phase 4 
Audit Trail
Every action in this lab was logged automatically by PIM. The audit history shows the complete lifecycle of the role assignment:

Admin created the eligible assignment
Test user submitted an activation request
Admin approved the request
Role was activated with timestamp, duration, and justification recorded

This audit trail is what satisfies compliance requirements in SOC 2, ISO 27001, and HIPAA. Every privileged action is documented with who did it, when, and why.
<img width="975" height="641" alt="image" src="https://github.com/user-attachments/assets/eaf48656-2ea5-4293-a99a-74199342a4c5" />


Phase 5 

Microsoft Sentinel Integration (In Progress)
Microsoft Sentinel workspace created. Microsoft Entra ID solution installed in Content Hub with 73 analytics rules and 11 playbooks deployed.
Next steps:

Connect Entra ID sign-in and audit logs to Sentinel workspace
Enable User and Entity Behavior Analytics
Write KQL detection rule for PIM role activation outside business hours
Build Logic App playbook to auto-revoke role on high severity alert



Key Concepts
Just-in-Time Access means privileged roles are not assigned permanently. A user who needs admin access must request it in the moment, explain why, pass MFA, and receive approval. The role is active for a short window then removed. This eliminates the attack surface created by standing privilege if an account is compromised, there is no persistent admin role to exploit.
Eligible vs Active Assignment an eligible assignment means the user can request the role but does not hold it. An active assignment means the user holds the role continuously. PIM best practice is to make all high-privilege roles eligible and reserve active assignments only for break-glass emergency accounts.
Zero Standing Privilege is the security model implemented in this lab. No user has admin access by default. Access is granted on demand, verified, time-limited, and logged. This is a core principle of Zero Trust architecture as defined in NIST 800-207.
