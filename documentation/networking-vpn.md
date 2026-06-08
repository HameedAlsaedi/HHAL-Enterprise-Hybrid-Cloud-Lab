# Hybrid Networking & Site-to-Site VPN Configuration

This document covers the detailed network topology, VLAN segmentation, and IPsec VPN parameters established between the On-Premises pfSense firewall and Microsoft Azure.

---

## 🏢 On-Premises Network (pfSense)

### 🌐 Interfaces & Assignments
* **WAN Interface (`em0`):** `192.168.100.197/24` (Receiving IP from physical ISP router)
* **LAN Interface (`em1`):** `192.168.1.1/24` (Connected to VirtualBox internal network `hhal-int`)

### 🏷️ VLAN Segmentation (802.1Q)
To simulate an enterprise environment, traffic is segmented into dedicated VLANs running over the physical `em1` interface:

| VLAN ID | Department/Purpose | Subnet Range | Gateway |
| :--- | :--- | :--- | :--- |
| **VLAN 10** | IT Department | `192.168.10.0/24` | `192.168.10.1` |
| **VLAN 20** | HR Department | `192.168.20.0/24` | `192.168.20.1` |
| **VLAN 30** | Servers Zone | `192.168.30.0/24` | `192.168.30.1` |

---

## ☁️ Azure Cloud Network

### 🌐 Virtual Networks (VNets)
1. **HHAL-VNet (Hub):** `10.0.0.0/16`
   * **Default Subnet:** `10.0.0.0/24`
   * **GatewaySubnet:** `10.0.1.0/27` (Dedicated for the VPN Gateway)
2. **HHAL-VNet02 (Spoke):** `172.16.0.0/16`
   * **Production Subnet:** `172.16.0.0/24`
   * **Target Host:** `VMLNX01` (`172.16.0.4`)

### 🔄 VNet Peering
* **Name:** `HHAL-Peering`
* **Configuration:** Hub-and-Spoke topology with **Gateway Transit** enabled on `HHAL-VNet`, allowing the Spoke VNet (`HHAL-VNet02`) to utilize the local network gateway to route traffic back to On-Premises.

---

## 🔒 Site-to-Site IPsec VPN Configuration

A secure, route-based IPsec VPN tunnel is established between the on-premises pfSense firewall and the Azure Virtual Network Gateway (`HHAL-VPN-GW`). This connection ensures seamless and encrypted hybrid routing across the entire infrastructure.

### 🔑 IKE Phase 1 (Security Association) Parameters
This phase negotiates the secure management channel between pfSense and Azure:

| Parameter | Configuration Value |
| :--- | :--- |
| **Key Exchange Version** | IKEv2 |
| **Authentication Method** | Mutual PSK (Pre-Shared Key) |
| **Encryption Algorithm** | AES 256-bit |
| **Hash / PRF Engine** | SHA256 |
| **Diffie-Hellman (DH) Group** | Group 2 (1024-bit) |

---

### 📦 IPsec Phase 2 (Data Associations) Parameters
To ensure full routing topology reachability across both interconnected Azure VNets (Hub & Spoke), two distinct Phase 2 cryptographic mappings are enforced on the pfSense firewall:

#### 🔹 Tunnel Gateway 01: Connect to Azure Hub VNet
* **Local Network (On-Prem Supernet):** `192.168.0.0/16` *(Covers VLANs 10, 20, and 30)*
* **Remote Network (Azure Hub):** `10.0.0.0/16`
* **Protocol:** ESP
* **Encryption / Hash:** AES256 / SHA256
* **Perfect Forward Secrecy (PFS):** Enabled (DH Group 2)

#### 🔹 Tunnel Gateway 02: Connect to Azure Spoke VNet (VNet02)
* **Local Network (On-Prem Supernet):** `192.168.0.0/16`
* **Remote Network (Azure Spoke):** `172.16.0.0/16`
* **Protocol:** ESP
* **Encryption / Hash:** AES256 / SHA256
* **Perfect Forward Secrecy (PFS):** Enabled (DH Group 2)
