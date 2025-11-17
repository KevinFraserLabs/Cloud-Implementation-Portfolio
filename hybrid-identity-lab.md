## 🚀 Hybrid Identity and Endpoint Management: On-Premises to Microsoft Intune 🚀

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

* **Organizational Unit (OU) Created:** The **`Users`** OU was created directly under the domain root (`KevinFraserLab.com`). This dedicated OU ensures the structure is ready for precise Group Policy linking and simplified filtering for Microsoft Entra Connect synchronization.

<img width="1625" height="792" alt="Screenshot 2025-11-13 200649" src="https://github.com/user-attachments/assets/c26a07df-43a9-4188-b2e1-b0fa7f3ec4bc" />


* **Test Users Created:** Two test user accounts, **`TestUser1`** and **`TestUser2`**, were created and placed within the new **`Users`** OU.

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
* **Configuration:** **Computer Configuration** $\rightarrow$ **Software Settings** $\rightarrow$ **Software Installation** was configured with the package set to **Assigned** and **Enforced**.
* **Verification:** Confirmed successful installation on the client machine following a forced GPO update and **Reboot** (installation occurs during computer startup).

<img width="1081" height="559" alt="Screenshot 2025-11-14 011805" src="https://github.com/user-attachments/assets/9db77a47-1aad-401e-a561-81229c3e280f" />

<img width="1028" height="770" alt="Screenshot 2025-11-14 011913" src="https://github.com/user-attachments/assets/6fbb3e0b-90c1-4775-b67d-77482cbe74a9" />


## **11. Cloud Identity Integration: Microsoft Entra Connect** ☁️

The synchronization software was installed directly on the **Domain Controller** (`DC` - `192.168.2.2`) to facilitate the hybrid identity infrastructure, synchronizing the on-premises Active Directory with Microsoft Entra ID (formerly Azure AD).

* **Installation Location:** Domain Controller (DC - `192.168.2.2`)
* **Software:** Installed the latest version of **Microsoft Entra Connect**.
* **Synchronization Configuration:**
    * **Authentication Method:** Configured for **Password Hash Synchronization (PHS)**.
    * **Synchronization Scope:** Set to synchronize the entire `KevinFraserLab.com` forest, specifically including the **`User Accounts`** OU for identity and the **`Client Devices`** OU for device objects.
* **Verification:**
    * Confirmed both **`TestUser1`** and **`TestUser2`** accounts appeared in the Microsoft Entra admin center with **"Windows Server AD"** as the source.

<img width="397" height="470" alt="Screenshot 2025-11-17 064748" src="https://github.com/user-attachments/assets/a2f13238-78a4-4532-bddd-b2a444fc5a87" />

<img width="1878" height="830" alt="Screenshot 2025-11-17 064840" src="https://github.com/user-attachments/assets/76ebe491-94aa-4c52-aa89-5942bfb6a1e6" />


---

### **12. Hybrid Entra ID Join and Group Writeback** 🤝

The Entra Connect configuration was extended to enable **Hybrid Entra ID Join (HAADJ)** for devices and **Group Writeback** for cloud-created groups.

* **Service Connection Point (SCP):** The Entra Connect wizard successfully configured the Service Connection Point (SCP) within the on-premises Active Directory.
* **Group Writeback:** Confirmed the writeback feature was enabled and successfully synchronized groups created in Entra ID back to the on-premises Active Directory.
* **Device Verification:**
    * After an AD synchronization cycle and client restart, both **`Windows1`** and **`Windows2`** successfully registered their identity with Entra ID.
    * Devices appeared in the Microsoft Entra admin center with a **Join Type** of **"Microsoft Entra Hybrid Joined"**.


<img width="1117" height="622" alt="Screenshot 2025-11-17 064911" src="https://github.com/user-attachments/assets/e299e0cf-73f8-4608-8a69-b00cda5c30a7" />

---

### **13. Intune Enrollment Prerequisites and GPO Fixes** 🛡️

Settings were configured in the cloud to allow enrollment.

* **MDM User Scope:** Configured in the Microsoft Entra admin center under **Mobility (MDM and MAM)** to set the **MDM User Scope** to **"All"**, ensuring all licensed users can auto-enroll.
* **Licensing:** An **Enterprise Mobility + Security E5** license was assigned to **`TestUser1`** (and **`TestUser2`** for testing) to meet the Intune management requirement.
* **GPO Timing Fix (Critical):** The **`Always wait for the network at computer startup and logon`** GPO was **Enabled** within the **Default Domain Policy** to prevent GPO failure due to network initialization timing issues.

<img width="1181" height="791" alt="Screenshot 2025-11-17 065310" src="https://github.com/user-attachments/assets/887b2ff5-a9cf-44de-82bd-6b68209f2b19" />

<img width="1063" height="744" alt="Screenshot 2025-11-17 065505" src="https://github.com/user-attachments/assets/89a59f8e-7b1e-42a6-849a-6aa92e3eda42" />


---

### **14. Group Policy Management: Automatic Intune Enrollment** ☁️

The GPO was configured to initiate the successful automatic enrollment of domain-joined devices into Microsoft Intune.

* **GPO Name:** `Intune AutoEnroll`
* **GPO Scope:** Linked directly to the **`Client Devices`** OU.
* **Policy Path:** **Computer Configuration** $\rightarrow$ **Policies** $\rightarrow$ **Administrative Templates** $\rightarrow$ **Windows Components** $\rightarrow$ **MDM**
* **Configuration:** The setting **`Enable automatic MDM enrollment using default Azure AD credentials`** was set to **Enabled** with the credential type set to **`User Credential`**. The **MDM Application ID** was correctly left **blank**.

<img width="1907" height="775" alt="Screenshot 2025-11-17 065839" src="https://github.com/user-attachments/assets/724c5dac-7320-47cf-98ab-4c6f0ca7da0f" />

---


### **15. Verification: Intune Enrollment and Management** ✅

This section confirms the successful automatic enrollment of the client devices into **Microsoft Intune**, completing the Hybrid Entra ID Join (HAADJ) and Intune co-management setup.

* **Intune Enrollment Status:** After a client reboot and user sign-in (using a licensed account like **`TestUser1`**), both **`Windows1`** and **`Windows2`** devices successfully completed the MDM auto-enrollment process.
* **Verification (Client-Side):**
    * **Access Work or School:** Confirmed the devices show a successful connection to **`KevinFraserLab.com`**'s work or school account, managed by **Microsoft Intune**.
    * **Command Prompt:** Running the command `dsregcmd /status` confirms both **`AzureAdJoined: YES`** and **`MdmManaged`** is populated (indicating Intune enrollment).

<img width="588" height="179" alt="Screenshot 2025-11-17 070251" src="https://github.com/user-attachments/assets/35c3aff6-9a62-48a6-84f1-94febb33c9a3" />
<img width="735" height="220" alt="Screenshot 2025-11-17 070319" src="https://github.com/user-attachments/assets/424129d1-df40-43d0-8abc-f3568145fa00" />


* **Verification (Intune Portal):**
    * The devices appeared in the **Microsoft Intune admin center** under **Devices** → **All devices**.
    * The **Managed by** column is confirmed as **"Intune"**.
    * The **Primary user** is correctly listed as the signed-in user (**`TestUser1`** or **`TestUser2`**).
    * The **Compliance** status is **"Compliant"**.

<img width="1882" height="540" alt="Screenshot 2025-11-17 070445" src="https://github.com/user-attachments/assets/603016ce-e76a-4c9c-84f6-39986f10eff6" />

<img width="1826" height="609" alt="Screenshot 2025-11-17 071739" src="https://github.com/user-attachments/assets/dfe3bca9-9365-46f0-8f51-e58d58bc1375" />


---

### **Preparation for Cloud Policy Assignment** ⚙️

To ensure clean and precise policy targeting, administrative **Security Groups** were created in Microsoft Entra ID based on function:

* **Device Group:** **`Client Devices`** (Used for policies targeting the machine, containing device objects `Windows1`and `Windows2`).
* **User Group:** **`Users`** (Created specifically for assigning user-centric policies, containing user objects like `TestUser1` and `TestUser2`).

<img width="1317" height="479" alt="Screenshot 2025-11-17 083718" src="https://github.com/user-attachments/assets/e8a742f2-839f-4da3-922d-9ecbef615fcf" />
<img width="1257" height="439" alt="Screenshot 2025-11-17 083731" src="https://github.com/user-attachments/assets/3bd5c106-15e4-4297-9824-51c3d3205e99" />
<img width="1254" height="523" alt="Screenshot 2025-11-17 083752" src="https://github.com/user-attachments/assets/43c6c56f-e198-4418-b631-fe34b11573a7" />


---

### **16. Cloud-Native Security Baseline and Configuration** 🛡️

With the client devices fully MDM-managed, governance was migrated from GPOs to Intune-deployed policies.

#### **Intune Device Compliance Policy (Security Baseline)**

A robust compliance policy was implemented, assigned to the **`Client Devices`** security group, to enforce a multi-layered Zero Trust security baseline.

* **Policy Name:** `Default Device Compliance Policy`
* **Assignment:** Targeted the **`Client Devices`** security group (containing device objects).
* **Key Requirements Enforced (All set to 'Require'):**
    * **Hardware Integrity (Device Health):** `Secure Boot` and `Code Integrity` to ensure a trusted boot environment.
    * **Data Protection (System Security):** `Require encryption of data storage on device` (BitLocker).
    * **Endpoint Protection (System Security):** `Firewall`, `Trusted Platform Module (TPM)`, `Antivirus`, and `Antispyware`.
    * **Threat Detection (Defender):** `Antimalware`, `Real-time monitoring`, and `Antimalware security intelligence up-to-date`.
    * **Risk-Based Access (MDE):** `Require the device to be at or under the machine risk score` set to **Low**.
* **Action for Non-Compliance:** Configured to immediately mark the device non-compliant and **send an email notification** to the end-user.
* **Verification:** The devices reported **"Compliant"** in the Intune admin center.

<img width="489" height="732" alt="Screenshot 2025-11-17 090939" src="https://github.com/user-attachments/assets/91b18624-5e71-4b9b-add2-7e10a4444ed9" />
<img width="442" height="446" alt="Screenshot 2025-11-17 090947" src="https://github.com/user-attachments/assets/f0e5c964-b107-486b-97f3-2bce3f213417" />
<img width="426" height="689" alt="Screenshot 2025-11-17 091124" src="https://github.com/user-attachments/assets/c3990ecc-698f-42c7-bd82-cc4ce0ae7a18" />


---

### **17. Application Management: Simple Device-Level Deployment (Microsoft To Do)** 💾

To demonstrate Intune's software distribution capability, the **Microsoft To Do** application was deployed using the simplified **Microsoft Store app (new)** method.

#### **A. Microsoft Store App Configuration**

* **Application:** Microsoft To Do
* **App Category:** **Productivity**
* **Install Context:** **System** (Ensures machine-level installation, independent of the user).
* **Assignment:** The app was assigned as **Required** to the **`Client Devices`** security group (containing device objects).

<img width="1294" height="765" alt="Screenshot 2025-11-17 084842" src="https://github.com/user-attachments/assets/86d4eb19-05c4-4ab6-9fd1-969547cc8017" />

#### **B. Verification**

The application installed silently and automatically onto both client devices following a successful Intune sync. 


<img width="895" height="533" alt="Screenshot 2025-11-17 085413" src="https://github.com/user-attachments/assets/24e80d99-5712-4e1d-8895-cbe35e42d6ff" />

<img width="1651" height="344" alt="Screenshot 2025-11-17 093532" src="https://github.com/user-attachments/assets/3f7ef199-3655-4756-819d-ae20ce14ad21" />


---

### **18. Conditional Access: Enforcing Zero Trust** 🔒

This final step integrates the Intune Compliance status with **Microsoft Entra Conditional Access** to enforce a security gate, demonstrating a fully managed, Zero Trust environment.

#### **A. Conditional Access Policy Configuration**

A policy was created to restrict access to Microsoft 365 services based on the device's Intune Compliance status.

* **Policy Name:** `Conditional Access Require Compliant Device`
* **Assignments:** Targeted the **`Test Users`** group and the **Office 365** cloud application.
* **Access Controls (Grant):** Set to **Require device to be marked as compliant** for Windows platforms.

<img width="1268" height="715" alt="Screenshot 2025-11-17 081855" src="https://github.com/user-attachments/assets/4cb99fa4-7521-45a5-9e2d-2458328a6b64" />

#### **B. Conditional Access Policy Confirmation**

Windows2 had Firewall disabled so it would fail compliance resulting in blocked access on the Microsoft365 Copilot App.

<img width="1679" height="619" alt="Screenshot 2025-11-17 094540" src="https://github.com/user-attachments/assets/28afe6de-2910-41b6-90b3-a2b8654d94a5" />

<img width="1016" height="710" alt="Screenshot 2025-11-17 095939" src="https://github.com/user-attachments/assets/a95e0765-19eb-4337-a29d-fdd7de9e1fb9" />

<img width="1002" height="685" alt="Screenshot 2025-11-17 100649" src="https://github.com/user-attachments/assets/e3a158ed-913b-4842-a737-373a3334fbb2" />
<img width="838" height="530" alt="Screenshot 2025-11-17 100716" src="https://github.com/user-attachments/assets/b0debdd0-6186-4b4c-b10a-4fab18646f6b" />

Once the Firewall was enabled, the device was no longer non compliant and was able to gain access.

<img width="996" height="587" alt="Screenshot 2025-11-17 100856" src="https://github.com/user-attachments/assets/0aaa57fd-33bf-4f62-9a55-10ae3466f886" />

<img width="987" height="658" alt="Screenshot 2025-11-17 100939" src="https://github.com/user-attachments/assets/1775094d-e335-49c7-8b87-127047f75e2e" />

And both Devices were now compliant on Intune.

<img width="1687" height="629" alt="Screenshot 2025-11-17 101135" src="https://github.com/user-attachments/assets/0f1e7c3b-7b58-42c7-b8b0-bd5ea19856f1" />


## **Conclusion** 🎉

The project successfully established a comprehensive **Hybrid Identity and Endpoint Management** solution. All goals were achieved: Hybrid Entra ID join, cloud co-management via Intune, replacement of legacy GPO software and policy management, and the final enforcement of a security baseline using **Conditional Access**. 
