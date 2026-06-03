# On-Premises Active Directory Configuration

This document provides the detailed configuration of the Active Directory Domain Services (AD DS) infrastructure implemented in the HHAL Enterprise Lab.

---

## 🆔 Domain Information
* **Domain Name:** `hhal.local`
* **Domain Controller:** `HHAL-DC01`
* **IP Address:** `192.168.30.10` (SERVERS VLAN 30)
* **Operating System:** Windows Server 2022 Standard

---

## 📂 Organizational Unit (OU) & Identity Structure

The directory is structured under a root OU named `HHAL_ROOT` to separate administrative and standard production resources:

```text
HHAL_ROOT/
│
├── 📁 HHAL_Admins/
│   └── 👤 HHAL Admin
│
├── 📁 HHAL_Users/
│   ├── 👤 Hameed IT (IT Department)
│   └── 👤 Hameed HR (HR Department)
│
├── 📁 HHAL_Groups/
│   ├── 👥 IT_Group (Global Security)
│   └── 👥 HR_Group (Global Security)
│
└── 📁 HHAL_Computers/
    ├── 🖥️ HHAL-DC01 (Domain Controller)
    ├── 🖥️ HHAL-SRV01 (WSUS & Backup)
    ├── 🖥️ HHAL-CL01 (Windows 11 Workstation)
    ├── 🖥️ HHAL-LNX01 (Ubuntu Server)
    └── 🖥️ HHAL-LX01 (Ubuntu Desktop)
