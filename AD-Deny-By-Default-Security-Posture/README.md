# Lab: Implementing a "Deny by Default" Security Posture
## Focus: Principle of Least Privilege & Zero Trust Access Control

### 1. Objective
The purpose of this lab was to transition the `josephlab.local` environment from a traditional "Default-Open" security baseline to a strict **"Deny by Default"** architecture. This ensures that users, systems, and network protocols are explicitly blocked from resources unless an authorized rule explicitly grants access.

### 2. Architectural Implementations

#### A. Identity & Access Management (Temporal Isolation)
Instead of allowing domain accounts 24/7 access, I configured temporal access boundaries to mitigate the risk of off-hours credential misuse.
* **Control:** Modified the `Logon Hours` attributes in Active Directory.
* **Posture:** All hours outside of standard business shifts are explicitly set to **Deny Access**. 
* **Verification:** Attempts to authenticate during restricted windows result in an immediate system rejection error: *"Your account has time restrictions that prevent you from logging on right now."*

#### B. Data Layer Isolation (The Manager-Vault)
Standard Windows environments inherit file permissions down the directory tree, often leaving shares loosely protected. 
* **Control:** Disabled inheritance on sensitive file paths (`C:\Manager_Vault`) and stripped out generic access groups like `Everyone` and `Authenticated Users`.
* **Posture:** Access is denied to all directory objects by default. 
* **Explicit Exceptions:** Only identities belonging to `Domain Admins` or specific managerial security groups are granted explicit read/write access descriptors.

#### C. Host Network Hardening (Port Auditing)
To ensure the Domain Controller wasn't exposing unnecessary attack surface, I performed a port-level host audit.
* **Control:** Executed network reconnaissance commands (`Get-NetTCPConnection`) to evaluate listening ports.
* **Posture:** Closed unrequired default listening services. Only explicit, hardened enterprise ports (such as DNS 53, Kerberos 88, and LDAP 389) remain authorized to handle incoming traffic.

### 3. Business & Security Impact
By enforcing "Deny by Default" across the identity, data, and network layers, the environment achieves:
1. **Attack Surface Reduction:** Minimizes the blast radius if an individual user account or network service is compromised.
2. **Regulatory Compliance:** Aligns the infrastructure with international security frameworks (such as ISO/IEC 27001 Access Control guidelines) which require strict least-privilege enforcement.
3. **Auditability:** Unauthorized access attempts generate clean event logs on the Domain Controller, making tracking and incident response significantly more efficient.

### 4. Verification Evidence
Below is the system-level validation showing the security architecture successfully intercepting and blocking unauthorized access attempts based on the hardening configurations:

![Temporal Access Denied Verification](./logon-denied-proof.png)
