# Linux Networking Lab (ipcalc, routing, firewall, DHCP, NAT)

## Overview

This repository documents a hands-on networking lab performed on Linux virtual machines.  
The lab focuses on practical IP addressing, routing, throughput testing, firewall policy design, log/traffic inspection, DHCP-based configuration, and NAT (SNAT/DNAT).

The work was executed in a controlled virtualized environment to simulate real-world network administration tasks.

---

## Environment

- **OS:** Ubuntu Server (no GUI)
- **Virtualization:** VirtualBox
- **Networking:** Internal networks + router VMs (Linux routing)
- **Core tooling:** ipcalc, iproute2, netplan, iptables, iperf3, nmap, tcpdump, traceroute, isc-dhcp-server, Apache2

---

## Learning Goals

- Understand IPv4 addressing, masks, prefixes, and network ranges
- Configure and validate static routes between hosts and through routers
- Measure throughput using iperf3 and correctly convert units
- Build firewall rules with different rule-ordering strategies
- Inspect routing/traffic using tcpdump and traceroute
- Deploy DHCP services and validate dynamic address assignment
- Implement NAT with SNAT (masquerade) and DNAT (port forwarding)

---

## Lab Structure

### Part 1 — `ipcalc`: Networks and Masks

**Tasks**
- Determine network address for a given IP/prefix
- Convert masks between:
  - dotted decimal ↔ prefix length
  - dotted decimal ↔ binary representation
  - binary mask ↔ dotted decimal & prefix
- Determine minimum/maximum host ranges for multiple masks
- Evaluate localhost accessibility for a set of IPs
- Classify given IPs as public vs private
- Validate candidate gateway IPs for a target network

**Outcome**
- Confident manipulation of subnetting and address ranges
- Clear understanding of loopback addressing (127.0.0.0/8)

---

### Part 2 — Static Routing Between Two Machines (ws1, ws2)

**Topology**
- Two VMs connected through an internal network interface

**Tasks**
- Inspect interfaces (`ip a`)
- Configure netplan:
  - ws1: `192.168.100.10/16`
  - ws2: `172.24.116.8/12`
- Apply configuration (`netplan apply`)
- Add static routes manually (`ip r add`)
- Persist static routes via `/etc/netplan/00-installer-config.yaml`
- Validate connectivity using `ping`

**Outcome**
- Ability to establish L3 connectivity across non-local networks using static routes

---

### Part 3 — `iperf3`: Throughput Measurement

**Tasks**
- Convert link rates:
  - Mbps ↔ MB/s
  - MB/s ↔ Kbps
  - Gbps ↔ Mbps
- Measure connection speed between ws1 and ws2 using iperf3 (server/client mode)

**Outcome**
- Practical measurement of network bandwidth and correct interpretation of units

---

### Part 4 — Firewall Policy (iptables) + Host Discovery (nmap)

**Tasks**
- Implement `/etc/firewall.sh` on ws1 and ws2:
  - Flush existing filter rules
  - Different rule ordering strategies:
    - ws1: *deny-first, allow-last*
    - ws2: *allow-first, deny-last*
  - Allow inbound access for ports **22 (SSH)** and **80 (HTTP)**
  - Control ICMP echo reply behavior via OUTPUT policy/rules
- Execute firewall scripts (`chmod +x`, then run)
- Demonstrate:
  - A host not responding to ping
  - `nmap` still reporting `Host is up`

**Outcome**
- Understanding of firewall rule order and default policies
- Practical awareness that ICMP reachability ≠ host availability

---

### Part 5 — Static Routing Across a Multi-Host Network

**Topology**
- 5 VMs: **ws11, ws21, ws22** + routers **r1, r2**
- Network name: `part5_network`

**Tasks**
- Configure all interfaces in netplan per topology diagram
- Enable IP forwarding:
  - temporary: `sysctl -w net.ipv4.ip_forward=1`
  - permanent: `/etc/sysctl.conf` → `net.ipv4.ip_forward = 1`
- Configure default routes (gateway) for workstations
- Add static routes on r1 and r2 via netplan
- Validate:
  - routing tables (`ip r`)
  - traffic traversal using `tcpdump`
- Use traceroute to map path and explain traceroute mechanism using tcpdump evidence
- Demonstrate ICMP error behavior by pinging a non-existent host and capturing ICMP via tcpdump

**Outcome**
- Ability to build static routing across multiple subnets and debug routing behavior

---

### Part 6 — DHCP: Dynamic IP Configuration

**Tasks**
- Configure DHCP server on r2 (`/etc/dhcp/dhcpd.conf`):
  - subnet, range
  - router option
  - DNS option
- Configure resolver (`resolv.conf` includes `nameserver 8.8.8.8`)
- Restart DHCP service (`systemctl restart isc-dhcp-server`)
- Reboot ws21 and verify it receives an IP (`ip a`)
- Configure a fixed MAC on ws11 via netplan and enable DHCP (`dhcp4: true`)
- Configure r1 DHCP similarly, but bind lease strictly to ws11 MAC (static mapping)
- Request lease renewal on ws21 and document IP before/after
- Explain which DHCP options were used (router, DNS, lease assignment logic)

**Outcome**
- Understanding of DHCP scope configuration, options, lease behavior, and MAC-based assignment

---

### Part 7 — NAT (SNAT/DNAT) + Apache Exposure

**Tasks**
- Configure Apache to listen publicly:
  - Update `/etc/apache2/ports.conf`: `Listen 0.0.0.0:80` on ws22 and r1
  - Start service: `service apache2 start`
- Implement firewall/NAT rules on r2 (script-based):
  - flush filter table
  - flush nat table
  - default FORWARD policy DROP
- Validate ICMP behavior across routed paths:
  - blocked then allowed
- Configure:
  - **SNAT (masquerade)** for local network behind r2 (10.20.0.0/?? as defined in topology)
  - **DNAT** for r2 port **8080** → ws22:80 (port forwarding)
- Validate TCP connectivity using telnet:
  - ws22 → r1 (SNAT path)
  - r1 → r2:8080 → ws22:80 (DNAT path)

**Outcome**
- Hands-on implementation of NAT for outbound masquerading and inbound port forwarding

---

## Evidence / Report Artifacts

This lab requires collecting evidence (screenshots) for:

- command outputs (`ip a`, `ip r`, `ping`, `iperf3`, `netstat`, `nmap`, `tcpdump`, `traceroute`, etc.)
- configuration files (netplan YAML, firewall scripts, dhcpd.conf, sysctl.conf, apache ports.conf)
- proof of connectivity and expected behavior (e.g., `0% packet loss`, `Host is up`)

---
