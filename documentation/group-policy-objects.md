# Enterprise Group Policy Objects (GPO) Management

This document outlines the design, implementation, and security baselines enforced via Group Policy Objects (GPOs) within the `hhal.local` domain infrastructure.

---

## 🏛️ GPO Inheritance & Scope
All policies are linked at the `HHAL_ROOT` Organizational Unit (OU) level to ensure uniform enforcement across all sub-OUs, with inheritance explicitly monitored to prevent policy conflicts.

---

## 📂 1. Drive Mapping & Storage Automation (`Drive-Mapping-GPO`)

This policy leverages **Group Policy Preferences (GPP)** to dynamically map network shares hosted on `HHAL-DC01` at user logon. Item-level targeting is utilized to ensure secure access control.

### 📊 Drive Mapping Reference Table
| Drive Letter | Share Path | Target AD Group | Permissions |
| :---: | :--- | :--- | :--- |
| **S:** | `\\hhal-dc01\Shares\General` | `Domain Users` | Read / Write |
| **I:** | `\\hhal-dc01\Shares\IT` | `IT_Group` | Full Control |
| **H:** | `\\hhal-dc01\Shares\HR` | `HR_Group` | Modify |

* **Action:** `Update` (Ensures the drive reconnects automatically at every logon state).
* **Options:** Enabled "Run in user's security context" to respect NTFS permissions.

---

## 🛡️ 2. Security Baseline & Centralized Updates (`HHAL-Baseline-Policy`)

This policy establishes the core security posture for all corporate computer nodes (`HHAL-CL01` and other target endpoints).

### 🌐 Network & DNS Configuration
* **DNS Enforcement:** Hardcodes the primary DNS client configuration to point directly to the local Domain Controller (`192.168.30.10`).
* **Windows Firewall:** Centralized enforcement of Windows Defender Firewall profiles (Domain, Private, Public) to allow authorized ICMP (Ping) and RDP traffic for remote IT management.

### 🔄 Windows Server Update Services (WSUS) Integration
Automates system update behaviors without requiring endpoint-user intervention:
* **Configure Automatic Updates:** Enabled - Option `4` (Auto download and schedule the install).
* **Scheduled Install Day:** Every Sunday at `03:00 AM`.
* **Specify Intranet Microsoft Update Service Location:** Points both update and statistics servers to:
  `http://HHAL-SRV01:8530`

---

## 🔍 Verification & Troubleshooting

To verify and force compliance of these policies on any client workstation (`HHAL-CL01`), the following commands are utilized:

* **Force Immediate Application:**
  ```bash
  gpupdate /force
