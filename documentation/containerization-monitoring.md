# Containerization & Infrastructure Monitoring Stack

This document details the centralized container architecture implemented within the HHAL Enterprise Lab to achieve visibility over application runtimes and environment workloads.

---

## 🖥️ Node Overview
* **Host Machine:** `HHAL-LNX01` (Ubuntu Server)
* **IP Address:** `192.168.30.12` (SERVERS VLAN 30)
* **Orchestration Tool:** Docker Community Edition (CE)

---

## 🐋 Centralized Management via Portainer CE

To streamline container operations and avoid command-line overhead, **Portainer Community Edition** is deployed. It acts as the primary visual gateway for controlling the microservice lifecycle and inspecting localized environments.

### ⚙️ Operational Specs
* **Image:** `portainer/portainer-ce:latest`
* **Exposed Web Ports:** `9000` (HTTP) / `9443` (Secure HTTPS)
* **Access URL:** `https://192.168.30.12:9443`

---

## 📊 Infrastructure Telemetry & Monitoring Capabilities

The container environment is designed to handle multi-tier service collection and performance metrics tracking without disrupting runtime operations:
1. **Real-time CPU Profiling:** Tracks thread distribution and utilization to mitigate application locks.
2. **Dynamic Memory Allocation:** Measures RAM consumption and swap space efficiency per container container.
3. **Network Interface Throughput (I/O):** Monitors egress and ingress traffic passing through virtual network bridges.
4. **Stateful Container Diagnostics:** Direct access to real-time container log streams for swift service troubleshooting.
