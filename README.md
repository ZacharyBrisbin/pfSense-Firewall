# 🔥 pfSense Firewall
![Status](https://img.shields.io/badge/Status-Completed-success)
![Firewall](https://img.shields.io/badge/Firewall-pfSense-orange)
![Virtualization](https://img.shields.io/badge/Virtualization-VirtualBox-blue)
![Network](https://img.shields.io/badge/Network-LAN-lightgrey)
![Servers](https://img.shields.io/badge/Servers-Windows%20Server-lightgrey)

## 📖 Overview

This lab demonstrates the deployment and configuration of **pfSense** as a perimeter firewall for a small enterprise lab environment.  

The firewall provides:

- WAN internet access
- LAN segmentation
- NAT functionality
- DNS forwarding
- Controlled outbound traffic

This setup serves as the network backbone for Active Directory, DNS, and client-server infrastructure labs.

---

## 🎯 Objectives

- Deploy pfSense as a virtual firewall
- Configure WAN and LAN interfaces
- Enable outbound NAT
- Configure firewall rules
- Enable DNS Resolver
- Provide secure internet access to internal clients
- Prepare environment for AD-integrated DNS

---

## 🖥️ Lab Environment

| Component | Role | IP Address |
|------------|------|------------|
| CorpFirewall | pfSense Gateway | 192.168.50.1 |
| DC01 | Domain Controller / DNS | 192.168.50.10 |
| CLIENT01 | Domain-Joined Workstation | DHCP |

**Internal Network:** `192.168.50.0/24`  
**Gateway:** `192.168.50.1`

---

## 🌐 Network Diagram


[ Home Router / Internet ]
|
(WAN)
CorpFirewall (pfSense)
WAN: DHCP
LAN: 192.168.50.1/24
|
-------------------
| |
DC01 CLIENT01
192.168.50.10 DHCP
DNS + AD


---

# ⚙️ Configuration Steps

---

## 1️⃣ Interface Assignment

During pfSense initial setup:

- Assign WAN to internet-facing adapter
- Assign LAN to internal adapter

---

## 2️⃣ Configure LAN Interface

Set LAN IP:


IP Address: 192.168.50.1
Subnet Mask: 255.255.255.0 (/24)


This becomes the default gateway for all internal devices.

---

## 3️⃣ Configure WAN Interface

Navigate to:

`Interfaces → WAN`

Set:

- IPv4 Configuration Type: DHCP
- Disable "Block private networks" (if behind NAT/home router)

Apply changes and verify WAN receives an IP address.

---

## 4️⃣ Verify Outbound NAT

Navigate to:

`Firewall → NAT → Outbound`

Ensure:

- Mode = **Automatic Outbound NAT**

This allows internal IPs to access the internet via NAT translation.

---

## 5️⃣ Configure Firewall Rules

Navigate to:

`Firewall → Rules → LAN`

Create rule:

| Setting | Value |
|----------|--------|
| Action | Pass |
| Interface | LAN |
| Protocol | Any |
| Source | LAN Net |
| Destination | Any |

Save and apply changes.

---

## 6️⃣ Enable DNS Resolver

Navigate to:

`Services → DNS Resolver`

Enable:

- ✔ DNS Resolver
- ✔ Allow LAN access

---

## 7️⃣ Configure Upstream DNS Servers

Navigate to:

`System → General Setup`

Add:


8.8.8.8
1.1.1.1


Save configuration.

---

# 🧪 Verification & Testing

From CLIENT01:

### Check IP Configuration

```bash
ipconfig /all
```

Expected:

DHCP assigned IP (192.168.50.x)

Gateway: 192.168.50.1

DNS: 192.168.50.10


---

### Test Gateway Connectivity

```bash
ping 192.168.50.1
Test Internet Access
ping 8.8.8.8
Test DNS Resolution
nslookup google.com
```

Successful resolution confirms:

Client → DC → pfSense → Internet DNS flow.

🔐 Hardening the Firewall

Instead of allowing all outbound traffic:

Allow DNS
Source	Destination	Port
LAN Net	Any 53
Allow Web Traffic Only
Source	Destination	Ports
LAN Net	Any	80, 443
Allow Remote Workstations
LAN Net Any 3389

Block all other outbound traffic.

🛠 Troubleshooting
Issue	Check
No internet	Verify NAT mode
Cannot resolve DNS	Check DNS Resolver service
Cannot reach gateway	Verify LAN IP configuration
WAN no IP	Confirm DHCP from home router
🧠 Skills Demonstrated

Firewall deployment

WAN/LAN configuration

Network Address Translation (NAT)

Firewall rule creation

DNS forwarding

Network troubleshooting

Enterprise lab backbone design
