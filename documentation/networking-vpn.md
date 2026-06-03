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

## 🔒 Site-to-Site IPsec VPN Policy

A secure, route-based policy-mimicking tunnel is established between pfSense and the Azure Virtual Network Gateway (`HHAL-VPN-GW`).

### 🔑 Phase 1 (IKE SA) Parameters
* **Key Exchange Version:** IKEv2
* **Authentication Method:** Mutual PSK (Pre-Shared Key)
* **Encryption Algorithm:** AES 256 bits
* **Hash / PRF:** SHA256
* **Diffie-Hellman (DH) Group:** Group 2 (1048 bit)

### 📦 Phase 2 & 3 (IPsec SA) Parameters
To ensure full routing across both Azure VNets, two distinct phase mappings are created on pfSense:

#### Phase 2: Tunnel to Hub VNet
* **Local Network:** `192.168.0.0/16` (Supernet covering all local VLANs)
* **Remote Network:** `10.0.0.0/16` (Azure Hub)
* **Protocol:** ESP
* **Encryption / Hash:** AES256 / SHA256
* **PFS Key Group:** Group 2 (1024 bit)

#### Phase 3: Tunnel to Spoke VNet02
* **Local Network:** `192.168.0.0/16`
* **Remote Network:** `172.16.0.0/16` (Azure Spoke)
* **Protocol:** ESP
* **Encryption / Hash:** AES256 / SHA256
* **PFS Key Group:** Group 2 (1024 bit)
