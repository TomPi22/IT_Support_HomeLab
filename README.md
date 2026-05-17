# IT Infrastructure, NOC & Security Foundations

A hands-on portfolio documenting the on-premises infrastructure, 
network security, and monitoring foundations required for 
Cloud Security and SOC Analyst roles in the EU market.

This repository covers Linux hardening, host-based security controls, 
enterprise monitoring (NOC), VPN architecture, and the foundational 
cloud identity bridge to the Azure SecOps portfolio.

Aligned with CompTIA Security+, GDPR (Art. 32), ITIL v4, and 
serving as the on-premises foundation for AZ-500 and SC-200 
certification paths.


## 🗺️ Strategic Roadmap & Project Index

*Click on a project to jump directly to the technical implementation.*

📁 **Phase 1: Core IT Infrastructure & Local Security (Foundations)**

* [Project 1: Linux File Server Implementation with Secure Role-Based Access Control (RBAC)](#project-1-linux-file-server-implementation-with-secure-role-based-access-control-rbac)
* [Project 2: Network Security - Host-Based Firewall (UFW) Implementation](#project-2-network-security---host-based-firewall-ufw-implementation)
* [Project 3: ITIL Incident Management & Network Troubleshooting](#project-3-itil-incident-management--network-troubleshooting)
* [Project 4: Secure Remote Access - WireGuard VPN Implementation](#project-4-secure-remote-access---wireguard-vpn-implementation)

📁 **Phase 2: Enterprise Observability & NOC Operations**

* [Project 5: Enterprise NOC Deployment (Zabbix 7.0 LTS & MariaDB)](#️-project-5-enterprise-noc-deployment-zabbix-70-lts--mariadb)

📁 **Phase 3: The Cloud Bridge (IAM & SIEM Fundamentals)**

* [Project 6: Cloud Identity & Access Management (IAM) - Microsoft Entra ID](#project-6-cloud-identity--access-management-iam---microsoft-entra-id)
* [Project 7: Cloud SIEM & Threat Detection - Microsoft Sentinel & KQL](#project-7-cloud-siem--threat-detection---microsoft-sentinel--kql)

🚀 **Next Step in the Roadmap:**
* For advanced Cloud Security Architecture (Projects 8 through 24), proceed to the [Azure Cloud Security Operations Repository](https://github.com/TomPi22/Azure-Cloud-Security-Operations).

|||

## Cloud Security Fundamentals (Cybersecurity Beginner)
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


## 🖥️ Project 5: Enterprise NOC Deployment (Zabbix 7.0 LTS & MariaDB)

**Objective:** Architect and deploy a centralized Network Operations Center (NOC) to establish continuous observability, telemetry ingestion, and event management across hybrid infrastructure.

**Framework & Role Alignment:**
* **ITIL v4:** Event Management & Incident Identification.
* **NIST CSF:** Detect (DE.AE) - Anomalies and Events.

### Phase 1: Execution
To simulate a production-grade on-premises foundation before bridging to the cloud, a full monitoring solution was deployed on Ubuntu Server 24.04.

1. **Infrastructure & Network Hardening:** The server was configured with a dedicated Bridged Network adapter to operate natively on the local LAN. A critical kernel-level bypass (GRUB bootloader manipulation) was executed to regain root access and restructure permissions during deployment.
2. **Database Resilience & Hardening:** MariaDB was deployed as the backend engine. To secure the database, a strict hardening script was executed (disabling remote root login, removing anonymous access). A "Clean Slate" architectural approach was utilized to drop and rebuild the SQL schemas, ensuring data integrity for the Zabbix telemetry.
3. **Application & Telemetry Pipeline:** Zabbix 7.0 LTS was installed. Overcoming hypervisor clipboard encoding conflicts, the backend database was successfully seeded with the Zabbix schema. The Apache/PHP frontend was then isolated, debugged, and launched to establish the web management console.

> *Proof of Architecture: Successful deployment of Zabbix 7.0 Web Frontend and Active NOC Dashboard.*

<img width="1478" height="966" alt="Captura de tela 2026-05-07 190446" src="https://github.com/user-attachments/assets/e10f6ace-953d-40f2-a15f-beee4d427f59" />

<img width="1917" height="1032" alt="Captura de tela 2026-05-07 192334" src="https://github.com/user-attachments/assets/40edabaa-17ad-4a6d-b4fc-b61574dd9848" />


**Skills Applied:** Linux Server Administration, Bare-Metal Troubleshooting (GRUB), Database Hardening (MariaDB), Zabbix 7.0 LTS, Network Routing, ITIL Event Management.

### Phase 2: Hybrid Endpoint Telemetry Integration (Windows Agent)

To prove cross-platform monitoring capabilities and hybrid network routing, the host physical machine (Windows) was integrated into the Linux-based NOC.

1. **Agent Deployment:** Zabbix Agent was installed on the Windows endpoint.
2. **Zero Trust & Network Isolation:** To ensure secure telemetry transmission across the NAT boundary, explicit Allow-List (Whitelist) policies were configured within the `zabbix_agentd.conf` file, strictly permitting traffic only from the NOC's designated gateway IP (`10.0.2.15`).
3. **Firewall Exception:** A precise inbound rule was injected via PowerShell to open TCP port 10050, allowing active/passive polling.
4. **Validation:** The endpoint was successfully registered via the Zabbix Web Console utilizing the NAT gateway loopback (`10.0.2.2`), immediately establishing a green ZBX availability status and streaming live telemetry (CPU, Memory, Disk I/O).

> *Proof of Execution: Active Dual-Telemetry on Zabbix NOC.*
<img width="1917" height="1031" alt="image" src="https://github.com/user-attachments/assets/351d428d-40d3-4c18-8842-785704fa368b" />


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

## project-7
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
