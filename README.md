# IT_Support_HomeLab

## Project 1: Linux File Server Implementation with Secure Role-Based Access Control (RBAC)

**1. Scenario (Business Problem)**
An AgTech company needed a central file server to share documents. The company has two main departments: **Finance** and **Field Operations** (Agronomists). 
The business rule is strict: 
* The Field team must have a shared folder to drop soil reports.
* The Finance team must have a secure folder for payroll. 
* *Security constraint:* The Field team cannot, under any circumstances, access the Finance folder.

**2. Topology**
* **OS:** Ubuntu Linux (Virtual Machine)
* **Service:** Samba (SMB Protocol)
* **Users/Groups:** * Group: `finance_grp` | User: `alice_fin`
  * Group: `field_grp` | User: `bob_agro`
* **Network:** Local Host Network

**3. Action (Implementation Steps)**
* Installed and enabled the Samba service.
* Created security groups and user accounts via CLI.
* Created directories `/srv/samba/finance` and `/srv/samba/field`.
* Applied strict Linux permissions (`chown` and `chmod`) to enforce department isolation.
* Configured the `/etc/samba/smb.conf` file to share the folders securely.

*(My Terminal Configuration)*
> <img width="813" height="731" alt="image" src="https://github.com/user-attachments/assets/c449ac06-5c9b-4356-b883-d8384119a452" />
<img width="812" height="730" alt="image" src="https://github.com/user-attachments/assets/35225ee0-4e1b-4e88-92f5-44b96342bb9a" />

**4. Conclusion / Test**
Simulated a login with the user `bob_agro` trying to access the Finance folder. The access was successfully **DENIED**, proving the security rules are working. Simulated `bob_agro` accessing the Field folder, and access was **GRANTED**.

*(Access Denied Proof)*

<img width="809" height="735" alt="image" src="https://github.com/user-attachments/assets/d9fd8fab-d6a2-4a5a-b6d6-933eaf17d6ba" />


## Project 2: Network Security - Host-Based Firewall (UFW) Implementation

**1. Scenario (Business Problem)**
Following the successful deployment of the AgTech file server, the infrastructure lacked network-level protection. The server was exposed to all internal network traffic, increasing the attack surface. To comply with basic cybersecurity frameworks and prevent unauthorized port scanning, a host-based firewall needed to be implemented following a "Default Deny" policy.

**2. Topology**
* **OS:** Ubuntu Linux
* **Security Tool:** UFW (Uncomplicated Firewall)
* **Allowed Services:** SSH (Port 22) for remote management, Samba (SMB) for file sharing.

**3. Action (Implementation Steps)**
* Verified the initial UFW status (inactive).
* Configured the baseline security policy: `default deny incoming` and `default allow outgoing`.
* Created explicit allow rules for **SSH** (to prevent lockout) and the **Samba** application profile.
* Enabled the firewall and activated the rules system-wide.

*(Firewall Configuration Commands)*
> <img width="821" height="581" alt="image" src="https://github.com/user-attachments/assets/b7f3a232-f2a4-4707-801a-1ad1c6b60cd4" />

**4. Conclusion / Test**
Executed `ufw status verbose` to verify the active ruleset. The firewall is now actively dropping unapproved traffic while permitting legitimate business operations (file sharing) and IT administration (SSH).

*(Active Firewall Ruleset Proof)*
> <img width="819" height="584" alt="image" src="https://github.com/user-attachments/assets/5a41b5f1-8b0a-4d6a-b552-996ebed6e971" />



