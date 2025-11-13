## 🚀 On-Premises Simulation with Cloud Migration

This document outlines the foundation build of the on-premises network, domain, and deployment infrastructure.

### **1. Virtual Machine (VM) and Network Setup**

* **Internal Switch:** Created on Host PC, named `KevinFraserLab`.
* **NAT Configuration:** Configured on Host PC using PowerShell.
* **Vethernet Adapter Static IP:** `192.168.2.1/24` set on the `vEthernet (KevinFraserLab)` adapter.

<img width="729" height="677" alt="Screenshot 2025-11-13 161715" src="https://github.com/user-attachments/assets/204e13d3-6281-42da-b551-a924fb7fe700" />

<img width="1197" height="895" alt="Screenshot 2025-11-13 161951" src="https://github.com/user-attachments/assets/fd201c8a-dbe4-448a-a283-c39edc53a9f7" />



---

### **2. Domain Control Server (DC)**

| Service | Configuration | IP Address |
| :--- | :--- | :--- |
| **VM Name** | DC (Created first VM) | `192.168.2.2` (Static) |
| **Operating System** | Windows Server 2022 (ISO used) | N/A |
| **Gateway** | `192.168.2.1` | N/A |
| **Public DNS** | `8.8.8.8` (Used for Internet Connectivity) | N/A |
| **Domain** | **Root Domain:** `KevinFraserLab.com` | N/A |

<img width="1920" height="1080" alt="Screenshot 2025-11-13 163217" src="https://github.com/user-attachments/assets/c395a230-cd70-45c8-86d5-14e92844d962" />


#### **Core Services Configuration**

* **Active Directory:** Enabled and server promoted to a Domain Controller.

<img width="1768" height="838" alt="Screenshot 2025-11-13 164251" src="https://github.com/user-attachments/assets/abaf4625-fadc-4234-90fe-0e6e25f7e075" />


* **DNS Server:** Enabled with Forward and Reverse Lookup Zones. Hostname and IP confirmed resolving.

<img width="1220" height="566" alt="Screenshot 2025-11-13 171625" src="https://github.com/user-attachments/assets/b008c3e0-2eaf-4a31-bdfe-9b10944bced5" />


* **DHCP Server:** Enabled.
    * **Scope Range:** `192.168.2.4` to `192.168.2.10`.

<img width="1903" height="1023" alt="Screenshot 2025-11-13 165722" src="https://github.com/user-attachments/assets/fbd07250-7980-4493-a55d-6aef02075493" />

  

---

### **3. Windows Deployment Server (WDS)**

* **VM Name:** Windows Deployment (WDS)
* **IP Address:** `192.168.2.3` (Static)
* **DNS:** `192.168.2.2` (Pointing to DC).

<img width="397" height="452" alt="Screenshot 2025-11-13 172125" src="https://github.com/user-attachments/assets/68e8361c-24e6-404c-a0ac-9895adc759c3" />


* **Domain Status:** Successfully joined to `KevinFraserLab.com` Domain.
* **Deployment Share:** Configured for PXE installation of Windows 11 unattended.

<img width="1386" height="682" alt="Screenshot 2025-11-13 173817" src="https://github.com/user-attachments/assets/4c2d75c0-656f-41d0-8695-b65084bec726" />

<img width="437" height="579" alt="Screenshot 2025-11-13 180448" src="https://github.com/user-attachments/assets/270b9e80-4649-4bab-bd21-4b5b487012bc" />


---

### **4. Deployment Configuration (Unattended Settings)**

The deployment process uses an unattended install with the following key settings:

| Setting | Value | Rationale |
| :--- | :--- | :--- |
| `Skip*` options | `YES` | Enables fully unattended installation. |
| `TaskSequenceID` | `Win11` | Specifies the installation task. |
| `JoinDomain` | `KevinFraserLab.com` | Joins the new domain. |
| `DomainAdmin` | `Administrator` | Domain administrator account used for joining. |
| `MachineObjectOU` | `CN=Computers,DC=KevinFraserLab,DC=com` | Ensures deployed clients land in the standard **Computers** container. |

<img width="767" height="558" alt="Screenshot 2025-11-13 192003" src="https://github.com/user-attachments/assets/f03750d7-e111-4957-93e1-00b806c1b8e5" />

<img width="1015" height="864" alt="Screenshot 2025-11-13 195014" src="https://github.com/user-attachments/assets/b04fe591-8705-4592-ba49-c258446548e1" />



---

### **5. Client Verification**

* **Client VMs:** `Windows1` and `Windows2`.
* **Deployment Status:** Installed Windows 11 unattended (only required entering the computer name).
* **Domain Status:** Successfully auto-joined the domain.
* **IP Leases Confirmed:** `Windows1` leased `192.168.2.4`, `Windows2` leased `192.168.2.5`.

<img width="1381" height="814" alt="Screenshot 2025-11-13 192806" src="https://github.com/user-attachments/assets/5e094148-5b71-4217-a695-2774f3362596" />

<img width="1052" height="752" alt="Screenshot 2025-11-13 194644" src="https://github.com/user-attachments/assets/54f4b181-7826-4eed-a931-1f60b6bb20cd" />

<img width="1360" height="878" alt="Screenshot 2025-11-13 195618" src="https://github.com/user-attachments/assets/b37d20f7-6646-4557-9140-17354dbf1892" />

<img width="1362" height="863" alt="Screenshot 2025-11-13 195644" src="https://github.com/user-attachments/assets/36d8e46b-9bcb-447a-a493-363fb64ae8a5" />

<img width="622" height="590" alt="Screenshot 2025-11-13 195705" src="https://github.com/user-attachments/assets/9959f0ba-9140-4b80-88ff-6261e8aae1a0" />


