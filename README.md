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
> **<img width="813" height="731" alt="image" src="https://github.com/user-attachments/assets/c449ac06-5c9b-4356-b883-d8384119a452" />
**
**<img width="812" height="730" alt="image" src="https://github.com/user-attachments/assets/35225ee0-4e1b-4e88-92f5-44b96342bb9a" />
**

**4. Conclusion / Test**
Simulated a login with the user `bob_agro` trying to access the Finance folder. The access was successfully **DENIED**, proving the security rules are working. Simulated `bob_agro` accessing the Field folder, and access was **GRANTED**.

*(Access Denied Proof)*
> **<img width="809" height="735" alt="image" src="https://github.com/user-attachments/assets/d9fd8fab-d6a2-4a5a-b6d6-933eaf17d6ba" />
**
