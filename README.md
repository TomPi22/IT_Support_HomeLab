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


## Project 5: Enterprise Monitoring & Compliance Infrastructure (Zabbix)

**1. Scenario & Compliance Context (GDPR & Security+)**
To comply with GDPR "Availability" requirements and CompTIA Security+ monitoring standards, I implemented a centralized Network Operations Center (NOC) using Zabbix. This setup ensures real-time visibility into system health, preventing data downtime—a critical aspect of technical data protection (Art. 32 GDPR).

**2. Tech Stack**
* **Monitoring Engine:** Zabbix 6.0 LTS
* **Database:** MariaDB (Relational Data Store)
* **Web Server:** Apache with PHP optimization
* **Security Frameworks:** Security+ (Performance Monitoring), GDPR (Availability/Resilience)

**3. Action (Implementation Steps)**
* Provisioned a MariaDB instance with a dedicated, non-root user for database security.
* Ingested the Zabbix SQL schema to support historical data retention.
* Configured the Zabbix Server to monitor local system resources (CPU, Memory, Network Latency).
* Hardened the service by managing standard Linux permissions and service states via `systemctl`.

*(Zabbix Initial Setup & Database Connection)*
<img width="1163" height="895" alt="image" src="https://github.com/user-attachments/assets/df60dd7c-07b3-4f97-986f-6dfa4de59dca" />



**4. Conclusion / Compliance Audit**
The monitoring dashboard is active and collecting telemetry. This provides the "Continuous Monitoring" capability required by SOC (Security Operations Center) roles in the EU market, allowing for rapid incident response as defined in the ITIL and Security+ domains.

*(Active NOC Dashboard Proof)*
<img width="1169" height="902" alt="image" src="https://github.com/user-attachments/assets/1aadc406-2463-4328-8ce7-a2834a92330c" />


## Project 6: Cloud Identity & Access Management (IAM) - Microsoft Entra ID

**1. Scenario (Business Problem)**
As the AgTech company expanded operations to Dublin, a new external Security Auditor joined the team. To comply with Zero Trust architecture and the Principle of Least Privilege (PoLP), this user required a cloud-native identity with strict read-only access to audit the Azure environment, preventing any unauthorized modifications to production resources.

**2. Tech Stack & Frameworks**
* **Cloud Provider:** Microsoft Azure
* **Identity Provider (IdP):** Microsoft Entra ID (formerly Azure AD)
* **Security Model:** Role-Based Access Control (RBAC)
* **Compliance:** Zero Trust / AZ-900 & AZ-500 Identity Standards

**3. Action (Implementation Steps)**
* Accessed the Microsoft Entra ID tenant in the Azure Portal.
* Provisioned a new cloud-only user (`dublin_auditor`).
* Generated a secure, auto-generated initial password.
* Applied Azure RBAC at the Directory level by assigning the **"Global Reader"** role, ensuring the user can view all administrative settings and configurations without edit capabilities.

*(Entra ID User Provisioning & RBAC Assignment)*
<img width="1706" height="786" alt="image" src="https://github.com/user-attachments/assets/0accdf5d-a48d-41a0-80ca-00b822ef7e15" />

*(Successfully navigated Azure Security Defaults by provisioning Microsoft Authenticator MFA for the auditor account)*
<img width="579" height="730" alt="image" src="https://github.com/user-attachments/assets/d77ac65a-8805-492e-9ba9-80c96cf3bdc7" />
<img width="1769" height="886" alt="image" src="https://github.com/user-attachments/assets/e8b70fe9-a6d6-464c-9b56-d0f7a662a9d3" />



**4. Conclusion**
The auditor's identity was successfully created and constrained by RBAC. A login simulation verified that the user can access the Azure Portal but is explicitly denied from creating, deleting, or modifying any resources, effectively mitigating insider threats and accidental misconfigurations.

*(Global Reader Access Validation Proof)*
<img width="1890" height="815" alt="image" src="https://github.com/user-attachments/assets/69da4f8c-a090-4aff-a7ec-c6681b64e791" />


## Project 7: Cloud SIEM & Threat Detection - Microsoft Sentinel & KQL

**1. Scenario (Business Problem)**
While RBAC policies successfully prevented unauthorized resource creation, the Security Operations Center (SOC) lacked visibility into these blocked attempts. The AgTech company needed a centralized SIEM to ingest cloud activity logs and automatically trigger incidents when suspicious or unauthorized actions occurred.

**2. Tech Stack & Frameworks**
* **Cloud SIEM:** Microsoft Azure Sentinel
* **Log Storage:** Log Analytics Workspace
* **Query Language:** KQL (Kusto Query Language)
* **Threat Detection:** Custom Analytics Rules mapping to the MITRE ATT&CK framework (Execution/Privilege Escalation).

**3. Action (Implementation Steps)**
* Activated Microsoft Sentinel and provisioned a Log Analytics Workspace in the EU region (North Europe).
* Configured the **Azure Activity** Data Connector to ingest subscription-level events in real-time.
* Authored a custom KQL (Kusto Query Language) query to hunt for "Access Denied" events caused by unauthorized users attempting to provision infrastructure.
* Deployed an Analytics Rule to automatically convert these specific KQL matches into high-severity SOC Incidents.

*(Sentinel KQL Query & Analytics Rule Configuration)*
<img width="1867" height="906" alt="image" src="https://github.com/user-attachments/assets/85f34752-754c-431e-943a-da297b85e73b" />

*(Successfully navigated the new Microsoft Unified SecOps Portal to deploy the Azure Activity solution)*
<img width="1869" height="899" alt="image" src="https://github.com/user-attachments/assets/9f7d6ab4-6ea9-46fb-a646-ab7ce0b18368" />

**4. Conclusion / SOC Simulation**
Simulated an insider threat scenario where the restricted `dublin_auditor` attempted to deploy a Virtual Machine. The RBAC policy blocked the action, the Azure Activity log recorded the failure, and Sentinel successfully ingested the log, triggering a real-time Incident in the SOC dashboard.

*(Sentinel Incident Triggered Proof)*
<img width="1893" height="906" alt="image" src="https://github.com/user-attachments/assets/1343c856-a980-49bc-9c5a-87e22dbeb7b1" />
