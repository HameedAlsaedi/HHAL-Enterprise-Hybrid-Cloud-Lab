# Enterprise Update Management & System Disaster Recovery

This document outlines the patching schedules and disaster recovery strategies deployed to safeguard data integrity and system consistency across the HHAL local domain infrastructure.

---

## 🖥️ Node Overview
* **Host Machine:** `HHAL-SRV01` (Windows Server 2022)
* **IP Address:** `192.168.30.11` (SERVERS VLAN 30)

---

## 🔄 Patch Management via Windows Server Update Services (WSUS)

To eliminate unnecessary WAN bandwidth consumption and secure client endpoints, WSUS is operationalized to cache update packages locally.

### ⚙️ Operational Blueprint
* **Products Target:** Windows 11 Workstations, Windows Server 2022 Core & Standard.
* **Classifications:** Critical Updates, Security Updates, and Definition Updates.
* **Storage Optimization:** Deployed metadata optimization and local caching configurations to prevent storage resource exhaustion.

---

## 💾 Backup Infrastructure & Disaster Recovery

Centralized system state backups protect against environmental data corruption and service failures.

### ⚙️ Operational Blueprint
* **Technology:** Windows Server Backup Feature.
* **Execution Schedule:** Daily at 12:00 AM.
* **Targets:** System State volumes and operational Active Directory configurations mapping `hhal.local`.
