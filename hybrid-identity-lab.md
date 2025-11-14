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


---

### **6. Active Directory Structure (User Accounts)**

* **Organizational Unit (OU) Created:** The **`User Accounts`** OU was created directly under the domain root (`KevinFraserLab.com`). This dedicated OU ensures the structure is ready for precise Group Policy linking and simplified filtering for Microsoft Entra Connect synchronization.

<img width="1625" height="792" alt="Screenshot 2025-11-13 200649" src="https://github.com/user-attachments/assets/c26a07df-43a9-4188-b2e1-b0fa7f3ec4bc" />


* **Test Users Created:** Two test user accounts, **`TestUser1`** and **`TestUser2`**, were created and placed within the new **`User Accounts`** OU.

<img width="1414" height="750" alt="Screenshot 2025-11-13 201113" src="https://github.com/user-attachments/assets/aca13b51-52c6-41e3-ad2c-d66209afee70" />


---

### **7. Group Policy Management: Advanced User Desktop Policy** 🖼️

The first GPO was implemented to enforce corporate desktop standards, display dynamic user context, and secure the desktop environment against user modifications.

* **GPO Name:** `User Profile Settings`
* **GPO Scope:** Linked to the **`Users`** OU.
* **Wallpaper Deployment:** Configured via a **User Logon Script** (`set_bg.bat`) running from **NETLOGON** to execute **BGInfo.exe** silently, displaying dynamic information (e.g., user name, IP address) on the desktop background.
* **Desktop Restrictions:**
    * **User Configuration** policies were applied to prevent changing the desktop background.
    * Additional policies were set to **lock down desktop personalization** and prevent users from accessing system settings (e.g., Registry Editor, specific Control Panel applets).
* **Verification:** Confirmed dynamic wallpaper applied and desktop personalization was fully restricted.

<img width="1915" height="1026" alt="Screenshot 2025-11-14 010648" src="https://github.com/user-attachments/assets/7792f71e-3fd0-46c0-8545-b692615d81a9" />

<img width="1028" height="780" alt="Screenshot 2025-11-14 010823" src="https://github.com/user-attachments/assets/07827dfd-7e06-47f7-806f-d8c2894e8734" />

---

### **8. Group Policy Management: Secure User Data Redirection** 🔒

The second GPO was implemented for data security, redirecting essential user folders to a centralized, secure server share.

* **GPO Name:** `User Data Redirection`
* **GPO Scope:** Linked to the **`Users`** OU.
* **Server Share:** `\\WDS\UserData$` was created on the WDS server.
* **NTFS Permissions:** Configured with granular security to grant **Authenticated Users** only the necessary rights to **create folders** but not read other users' data (granting the user **exclusive rights** to their redirected folder).
* **Redirection Target:** `Documents` and `Desktop` folders.
* **Verification:** Confirmed files saved to Desktop/Documents followed the user across client machines and were stored on the `\\WDS\UserData$` share.

<img width="793" height="537" alt="Screenshot 2025-11-13 233719" src="https://github.com/user-attachments/assets/59781fdd-a920-4380-a082-46b92d64de9d" />

<img width="1023" height="771" alt="Screenshot 2025-11-14 011049" src="https://github.com/user-attachments/assets/006211d0-d825-4558-9afb-9696c7603370" />

<img width="1216" height="652" alt="Screenshot 2025-11-14 011006" src="https://github.com/user-attachments/assets/5d4579c8-885a-48c0-aefc-8af70106bd3d" />


---

### **9. Active Directory Structure Optimization** 💡

The default Active Directory structure was reorganized for clean GPO linking and professional management practices.

* **Final OU Structure:** Computer objects were moved from the default **Computers** container into dedicated OUs under a single organizational root (e.g., `$KevinFraserLab`):
    * `$KevinFraserLab` $\rightarrow$ **`Devices`** $\rightarrow$ **`Client Devices`** (for client PCs).
    * `$KevinFraserLab` $\rightarrow$ **`Devices`** $\rightarrow$ **`Servers`** (for WDS, DC, etc.).
* **Rationale:** This structure allows **GPO linking** to be precise (e.g., client software GPOs only link to **`Client Devices`**) without using complex Security Filtering.

<img width="449" height="511" alt="Screenshot 2025-11-14 011500" src="https://github.com/user-attachments/assets/4cd56eb8-1677-457c-a19e-ddac20642edd" />

---

### **10. Group Policy Management: Software Deployment** 📦

The final GPO was configured for mandatory, silent software installation using the optimized Computer OU structure.

* **GPO Name:** `GPO_Software_7Zip` (or similar).
* **GPO Scope:** Linked directly to the **`Client Devices`** OU.
* **Software Distribution Point:** The MSI file was placed in the central, accessible **SYSVOL** share: `\\DC\SYSVOL\KevinFraserLab.com\SoftwareDeploy\7zip.msi`.
* **Configuration:** **Computer Configuration** $\rightarrow$ **Software Settings** $\rightarrow$ **Software Installation** was configured with the package set to **Assigned**.
* **Verification:** Confirmed successful installation on the client machine following a forced GPO update and **Reboot** (installation occurs during computer startup).

<img width="1081" height="559" alt="Screenshot 2025-11-14 011805" src="https://github.com/user-attachments/assets/9db77a47-1aad-401e-a561-81229c3e280f" />

<img width="1028" height="770" alt="Screenshot 2025-11-14 011913" src="https://github.com/user-attachments/assets/6fbb3e0b-90c1-4775-b67d-77482cbe74a9" />




