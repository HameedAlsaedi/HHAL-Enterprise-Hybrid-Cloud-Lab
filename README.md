# HHAL Hybrid Cloud Project

An enterprise-grade Hybrid Cloud Infrastructure project demonstrating the integration of an On-Premises virtualized environment (via VirtualBox & pfSense) with Microsoft Azure using a secure Site-to-Site (IPsec) VPN and Entra ID identity synchronization.

---

## ☁️ Azure Architecture

### Resource Group: `HHAL-RG`
* **Subscription:** `HHAL-Azure-Subscription`

### 🌐 Networking
* **Hub VNet:** `HHAL-VNet` (Address Space: `10.0.0.0/16`)
  * **Subnet:** `10.0.0.0/24`
  * **GatewaySubnet:** `10.0.1.0/27`
* **Spoke VNet:** `HHAL-VNet02` (Address Space: `172.16.0.0/16`)
  * **Subnet:** `172.16.0.0/24`
* **Peering:** `HHAL-Peering` (Configured between Hub & Spoke with Gateway Transit enabled).

### 🔒 VPN Gateway & Connectivity
* **Virtual Network Gateway:** `HHAL-VPN-GW` (Generation 1, SKU: `VpnGw1AZ`, Route-based)
* **Local Network Gateway:** `HHAL-LNG` (Targeting On-Prem Public IP, Address Space: `192.168.0.0/16`)
* **Connection:** `HHAL-Azure-to-On-prem` (Site-to-Site IPsec)
  * **IKE Phase 1:** AES256, SHA256, DH Group 2
  * **IKE Phase 2 (IPsec):** AES256, SHA256, PFS2

### 🖥️ Virtual Machines
* **VMLNX01:** Ubuntu Server (IP: `172.16.0.4` inside `HHAL-VNet02`)

---

## 🏢 On-Premises Infrastructure (VirtualBox)

### 🛡️ Firewall / Router: `HHAL-FW01` (pfSense)
* **Interfaces:**
  * **WAN (em0):** `192.168.100.197/24` (Bridged to Physical ISP Router)
  * **LAN (em1):** `192.168.1.1/24` (Internal Network: `hhal-int`)
  * **VLAN 10 (IT):** `192.168.10.1/24`
  * **VLAN 20 (HR):** `192.168.20.1/24`
  * **VLAN 30 (SERVERS):** `192.168.30.1/24`

### 🆔 Identity & Core Services

#### 1. Domain Controller: `HHAL-DC01` (Windows Server 2022)
* **IP Address:** `192.168.30.10` (VLAN 30)
* **Roles:** Active Directory Domain Services (Domain: `hhal.local`), DNS, DHCP.
* **Active Directory Structure:**
  * `HHAL_Admins` (HHAL Admin)
  * `HHAL_Users` (Hameed IT, Hameed HR)
  * `HHAL_Groups` (IT_Group, HR_Group)
* **Group Policy Objects (GPOs):**
  * `Drive-Mapping-GPO` (S: Shared, I: IT, H: HR)
  * `HHAL-Baseline-Policy`

#### 2. Management & Backup: `HHAL-SRV01` (Windows Server 2022)
* **IP Address:** `192.168.30.11`
* **Roles:** WSUS (Auto updates), Scheduled Backups (12 AM).
* **Hybrid Link:** Microsoft Entra Connect Sync Agent (Syncs On-Prem AD to Entra ID).

#### 3. Linux Services: `HHAL-LNX01` (Ubuntu Server)
* **IP Address:** `192.168.30.12`
* **Roles:** Docker Engine & Environment Monitoring.

#### 4. Workstations (DHCP Clients)
* **Windows 11:** `HHAL-CL01` (Joined to `hhal.local`)
* **Ubuntu Desktop:** `HHAL-LX01` (Joined to `hhal.local`)
