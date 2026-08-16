# Isolated-Perimeter-Defense-Granular-Web-Filtering-Lab-pfSense-RHEL
Network perimeter defense lab showcasing zero-trust segmentation, Layer 4 stateful packet inspection, and Layer 7 domain allowlisting using virtualized pfSense and Red Hat Enterprise Linux.
# Isolated Perimeter Defense & Granular Web Filtering Lab

An end-to-end network security lab demonstrating hypervisor network segmentation, stateful packet filtering, and Layer 7 granular access control using a **pfSense Community Edition Firewall** and a **Red Hat Enterprise Linux (RHEL)** client.

---

## 📌 Architecture & Traffic Topology

```text
+-------------------------------------------------------------------------+
|                              HYPERVISOR                                 |
|                                                                         |
|  +--------------------+                   +--------------------------+  |
|  |     RHEL Client    |                   |     pfSense Firewall     |  |
|  |    (Test Endpoint) |                   |    (Gateway & Security)  |  |
|  |                    |                   |                          |  |
|  | IP: 192.168.100.10 |                   | LAN: 192.168.100.1/24    |  |
|  | Gateway: .1        |                   | WAN: DHCP / Bridged      |  |
|  +---------+----------+                   +----+----------------+----+  |
|            |                                   |                |       |
|            +======= [ Isolated VNet / LAN ] ===+                |       |
+-----------------------------------------------------------------|-------+
                                                                  |
                                                           [ Physical LAN ]
                                                                  |
                                                            ( Internet )
