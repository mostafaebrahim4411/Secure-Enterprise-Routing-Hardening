# Secure Multi-Branch Enterprise Network Topology (EVE-NG)

## 🌐 Executive Summary
This project demonstrates the design and implementation of a secure, production-ready enterprise network topology using **EVE-NG**. The architecture simulates a multi-branch corporate environment that balances dynamic scalability with rigorous infrastructure hardening and access control. 

The network establishes full convergence between multiple local segments while systematically enforcing the principle of least privilege and control plane security.

---

## 🗺️ Network Topology
Below is the architectural diagram of the implemented network (Referenced as **Annotation 2026-05-28 025247_2.png**):

![Network Topology](topology.png)

### 📋 IP Addressing Scheme
| Device | Interface | IP Address | Subnet Mask / CIDR | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **R3** | e0/0 | 10.1.1.1 | 255.255.255.252 (/30) | WAN Link to Core (R5) |
| **R3** | e0/1 | 192.168.1.1 | 255.255.255.0 (/24) | Gateway for Branch A LAN |
| **R4** | e0/0 | 10.2.2.1 | 255.255.255.252 (/30) | WAN Link to Core (R5) |
| **R4** | e0/1 | 192.168.3.1 | 255.255.255.0 (/24) | Gateway for Branch B LAN |
| **R5** | e0/0 | 10.1.1.2 | 255.255.255.252 (/30) | WAN Link to R3 |
| **R5** | e0/1 | 10.2.2.2 | 255.255.255.252 (/30) | WAN Link to R4 |
| **R5** | e0/2 | 192.168.20.139 | 255.255.255.0 (/24) | Edge Internet Gateway (NAT) |

---

## 🛠️ Key Features & Implemented Controls

### 1. Routing & Core Infrastructure
* **OSPFv2 Routing:** Implemented single-area OSPF across all three routers to achieve full network convergence, enabling dynamic, automated path determination between Branch A (`192.168.1.0/24`) and Branch B (`192.168.3.0/24`).
* **Centralized Edge NAT:** Configured Router R5 as an Internet Edge Gateway, utilizing dynamic NAT/PAT to securely translate internal private IP segments into public-facing IPs through interface `e0/2`.
* **Dynamic IP Allocation:** Configured local DHCP pools on branch routers to streamline end-device provisioning.

### 2. Control Plane & Infrastructure Hardening
* **OSPF MD5 Authentication:** Secured the routing domain by enforcing Message-Digest (MD5) authentication on all neighbor-facing interfaces. This mitigates unauthorized route injections and rogue router attacks.
* **Secure Management (SSHv2):** Completely disabled unencrypted Telnet across all network elements and enforced SSHv2 globally, protecting administrative traffic from credential sniffing.
* **Strict Password Policies:** Leveraged the high-security **scrypt** cryptographic hashing algorithm (Type 9) for user secrets, backed by a global configuration policy enforcing a minimum password length of 10 characters.
* **VTY and Console Line Protection:** Bound interactive management sessions to local database authentication and enabled `logging synchronous` on terminal lines to prevent log interruption during configuration tasks.

### 3. Role-Based Access Control (RBAC) via Parser Views
To prevent unauthorized or accidental production changes, Cisco **Parser Views** were implemented to enforce proper separation of duties:
* **Admin View:** Grants full operational and global configuration privileges to authorized network administrators.
* **Student/Operator View:** A strictly restricted view that isolates access to safe, non-disruptive `show` monitoring commands, explicitly denying access to `configure terminal` or disruptive modification utilities.

---

## 📂 Project Structure
* `/configs/` - Contains production-ready running configuration scripts for R3, R4, and R5.
* `topology.png` - High-resolution network architecture diagram.
* `secure_lab.unl` - Exported EVE-NG lab file for deployment and replication.

