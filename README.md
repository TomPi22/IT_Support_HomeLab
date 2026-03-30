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

Secure and auditable server.


## Project 3: ITIL Incident Management & Network Troubleshooting

**1. Scenario (Business Problem)**
A high-priority ticket was opened by the Field Operations team. An agronomist working remotely reported a sudden loss of connection to the `Field_Reports` shared folder. The business impact was high as daily soil analysis uploads were halted.

**2. Troubleshooting Methodology**
Following a logical bottom-up approach (OSI Model):
* **Layer 3 (Network):** Verified if the server was reachable via ICMP (Ping). The server was responding, isolating the issue from a general network outage.
* **Layer 4 (Transport):** Tested the specific SMB port (445) connectivity using `Netcat` (`nc -zv`). The connection was actively refused.
* **Security Audit:** Checked the host-based firewall (`ufw status numbered`). 

**3. Action (Resolution)**
Identified an erroneous "DENY" rule blocking the Samba service ports. 
* Deleted the misconfigured firewall rule.
* Reloaded the firewall policies.
* Re-tested port 445 connectivity, which returned a `succeeded` status.

*(Troubleshooting Commands & Firewall Audit)*
> <img width="815" height="576" alt="image" src="https://github.com/user-attachments/assets/177003e3-4fe6-412e-b0f8-123d9c1c17b2" />

**4. Conclusion / Test**
The Incident was resolved within the SLA. The field team successfully re-authenticated to the Samba share, confirming service restoration. 

*(Service Restored Proof)*
> <img width="920" height="588" alt="image" src="https://github.com/user-attachments/assets/dac5a80e-1b90-4340-880f-0bdf5a1f5dd1" />


## Project 4: Secure Remote Access - WireGuard VPN Implementation

**1. Scenario (Business Problem)**
The AgTech field team required secure, encrypted access to the internal file server while traveling or working from untrusted public networks (e.g., airports, cafes). Exposing the Samba server directly to the internet was a critical security risk. 

**2. Topology**
* **VPN Protocol:** WireGuard (Layer 3)
* **Encryption:** ChaCha20 for symmetric encryption, Curve25519 for key exchange.
* **Network:** VPN Subnet `10.0.0.0/24`
* **Firewall (UFW):** UDP Port `51820` opened for encrypted tunnel traffic.

**3. Action (Implementation Steps)**
* Installed WireGuard tools on the Ubuntu Server.
* Generated public and private cryptographic keys.
* Created the WireGuard interface configuration file (`wg0.conf`), defining the server's private IP (`10.0.0.1`) and listening port (`51820`).
* Updated the host-based firewall (UFW) to accept incoming UDP traffic on the VPN port.
* Initialized the VPN interface using `wg-quick`.

*(Cryptographic Keys & Config)*
> <img width="954" height="582" alt="image" src="https://github.com/user-attachments/assets/1ce6a864-924e-4441-bdba-cf849dbff9d2" />


**4. Conclusion / Test**
Executed `wg show` to audit the active VPN tunnel. The interface `wg0` is successfully running, securely listening for authenticated client handshakes.

*(Active VPN Interface Proof)*
> <img width="955" height="578" alt="image" src="https://github.com/user-attachments/assets/f5cd3cc2-c170-4a57-9518-ce5986359885" />

