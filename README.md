# Enterprise Network Lab — Cisco Packet Tracer


![Network Topology](topology.png)

A simulated enterprise network built in Cisco Packet Tracer, designed to reflect real-world mid-size company infrastructure. The project covers VLAN segmentation, inter-VLAN routing, DMZ architecture, OSPF dynamic routing, and ACL-based traffic control.

---

## Network Topology
```
                        [ R1 ]
                       /      \
                   [SW1]      [DMZ-SW]
                /   / | \        |    |    |
             SW2  SW3 SW4 SW5  Web  DNS  Mail
              |    |   |   |
            IT   HR  Sales Mgmt
```

---

## VLAN Design

| VLAN | Name       | Subnet          | Purpose                  |
|------|------------|-----------------|--------------------------|
| 10   | IT         | 10.10.10.0/24   | IT department clients    |
| 20   | HR         | 10.10.20.0/24   | HR department clients    |
| 30   | Sales      | 10.10.30.0/24   | Sales department clients |
| 40   | Management | 10.10.40.0/24   | Network administration   |
| 100  | DMZ        | 172.16.0.0/24   | Public-facing servers    |

---

## Device Inventory

| Device   | Model         | Role                        |
|----------|---------------|-----------------------------|
| R1       | Cisco 2911    | Edge router, DMZ gateway    |
| SW1      | Cisco 3650    | L3 core switch, inter-VLAN  |
| SW2      | Cisco 2960    | Access switch — IT          |
| SW3      | Cisco 2960    | Access switch — HR          |
| SW4      | Cisco 2960    | Access switch — Sales       |
| SW5      | Cisco 2960    | Access switch — Management  |
| DMZ-SW   | Cisco 2960    | Access switch — DMZ servers |

---

## Design Decisions

**Collapsed Core (2-tier) Architecture**
A full 3-tier design was intentionally avoided. Collapsed core is standard in mid-size enterprises (50–500 users) and is more representative of real-world environments at this scale.

**L3 Switch as Core Routing Point**
Inter-VLAN routing is handled by SW1 (Layer 3 switch) using SVIs, not by the edge router. This follows industry best practice — the router handles only external and DMZ traffic, while the L3 switch handles all internal routing.

**Isolated DMZ**
DMZ-SW connects directly to R1, not to SW1. This ensures the DMZ is isolated from the internal network. All traffic between internal VLANs and DMZ passes through R1, where ACLs can enforce strict access control.

**Management VLAN Isolation**
VLAN 40 is dedicated to network device management. VTY access on SW1 is restricted to VLAN 40 only, preventing unauthorized management access from other departments.

**OSPF Dynamic Routing**
OSPF (Area 0) is configured between R1 and SW1 to dynamically exchange routing information. This eliminates the need for manual static routes and reflects real enterprise routing practice.

---

## ACL Policy

| Rule | Source | Destination | Action |
|------|--------|-------------|--------|
| Block HR → IT | 10.10.20.0/24 | 10.10.10.0/24 | Deny |
| Allow HTTP to DMZ | Any | 172.16.0.0/24 port 80 | Permit |
| Allow DNS to DMZ | Any | 172.16.0.0/24 port 53 | Permit |
| Block SSH to DMZ | Any | 172.16.0.0/24 port 22 | Deny |
| Restrict VTY access | Non-VLAN40 | SW1 management | Deny |

---

## Verification

| Test | Command | Expected Result |
|------|---------|-----------------|
| Inter-VLAN routing | `ping 10.10.20.10` from IT-PC1 | Success |
| OSPF adjacency | `show ip ospf neighbor` on SW1 | Full state with R1 |
| DMZ HTTP access | Web browser to 172.16.0.10 | Webpage loads |
| HR blocked from IT | `ping 10.10.10.10` from HR-PC1 | Unreachable |
| VTY restriction | `telnet 10.10.40.1` from HR-PC1 | Connection refused |

---

## Skills Demonstrated

- VLAN configuration and trunking (802.1Q)
- Layer 3 switching and SVI configuration
- OSPF dynamic routing
- ACL design and implementation
- DMZ network architecture
- Network segmentation and security principles
- Cisco IOS CLI
