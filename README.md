# Isolated-Perimeter-Defense-Granular-Web-Filtering-Lab-pfSense-RHEL
Network perimeter defense lab showcasing zero-trust segmentation, Layer 4 stateful packet inspection, and Layer 7 domain allowlisting using virtualized pfSense and Red Hat Enterprise Linux.
# Isolated Perimeter Defense & Granular Web Filtering Lab

An end-to-end network security lab demonstrating hypervisor network segmentation, stateful packet filtering, and Layer 7 granular access control using a **pfSense Community Edition Firewall** and a **Red Hat Enterprise Linux (RHEL)** client.


---------
### Key Topology Attributes

* **Network Isolation:** The RHEL endpoint has zero direct access to the physical LAN or Internet adapter.
* **Single Path Egress:** All ingress and egress traffic traverses the pfSense virtual appliance.
* **Default Posture:** Default-Deny for all non-approved protocols and destinations.

---

## ⚙️ Project Highlights & Features

* **Zero-Trust Network Segmentation:** Decoupled internal test host from the host machine's physical network adapter using a dedicated Host-Only/Internal Virtual Switch.
* **Stateful Layer 4 Protocol Hardening:** Enforced least-privilege egress policies:
  * **Allowed:** DNS (`UDP/TCP 53`), HTTP (`TCP 80`), HTTPS (`TCP 443`).
  * **Blocked:** ICMP, SSH, FTP, Telnet, and all arbitrary outbound TCP/UDP traffic.
* **Layer 7 Granular Web Filtering:**
  * Implemented domain-level and category-based allowlisting for mission-critical portals (Government, Banking/Finance, and Healthcare).
  * Enforced blanket drops for entertainment, social media, untrusted, and uncategorized domains.
* **Traffic Inspection & Verification:** Monitored state tables, firewall live logs, and DNS resolution logs to validate rule processing order.

---

## 🚀 Implementation & Configuration Walkthrough

### 1. Network Interface Provisioning

**pfSense Virtual Appliance Interfaces:**
* **WAN Interface:** Attached to Bridged/NAT Network (Obtains external uplink via DHCP).
* **LAN Interface:** Attached to `Isolated-Internal-Network` (Static IP: `192.168.100.1/24`).

**RHEL Endpoint Network Configuration:**

``` 
bash
nmcli connection modify eth0 ipv4.addresses 192.168.100.10/24
nmcli connection modify eth0 ipv4.gateway 192.168.100.1
nmcli connection modify eth0 ipv4.dns "192.168.100.1"
nmcli connection modify eth0 ipv4.method manual
nmcli connection up eth0
```
### 2. Firewall Rule Base (pfSense LAN Interface)

Rules are evaluated from top to bottom (first-match basis):
PriorityActionProtocolSourcePortDestinationPortDescription
1PassUDP/TCP192.168.100.10*192.168.100.153Allow DNS to pfSense Resolver
2PassTCP192.168.100.10*Alias_Allowed_Sites80, 443Permit Allowed Web Categories
3BlockTCP192.168.100.10**80, 443Block Non-Approved Web Destinations
4BlockAny192.168.100.10***Default Deny (Drop all non-web traffic)

### 3. Verification & Validation Testing

Execute the following test battery from the RHEL endpoint terminal:
Test A: Approved Web Traffic (Expected: HTTP 200 / Success)

curl -I https://www.incometax.gov.in
curl -I https://www.onlinesbi.sbi

Test B: Restricted Web Traffic (Expected: Connection Timed Out / Dropped)

curl -I --connect-timeout 5 https://www.facebook.com
curl -I --connect-timeout 5 https://www.gaming-site.com

Test C: Non-Web Protocol Egress (Expected: Blocked by Default-Deny)

# ICMP Ping Test
ping -c 3 8.8.8.8

# Outbound SSH Test
ssh -v -o ConnectTimeout=5 user@test.rebex.net

Skills & Tools Demonstrated
Security Tools: pfSense CE, Stateful Packet Filtering, DNSBL / pfBlockerNG.
Operating Systems: Red Hat Enterprise Linux (RHEL 9), Linux Networking CLI (nmcli, ip, ss, curl).
Networking Concepts: Virtual Switches, Network Isolation, NAT/PAT, Rule Order Logic, DNS Resolution.

```

## 📌 Architecture & Traffic Topology

```text
-------------------------------------------------------------------------+
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

