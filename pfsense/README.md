# pfSense (Firewall/Gateway) — Mini SOC Home Lab

This folder documents my **pfSense** deployment used as the **gateway + DHCP server** for the lab network. pfSense provides routing/NAT to the internet (via VirtualBox NAT on WAN) and a private LAN for my SOC VMs (Wazuh, Suricata, Windows, Kali, Ubuntu).

---

## Lab topology (high level)

**Internet (VirtualBox NAT)** → **pfSense WAN (em0)** → **pfSense LAN (em1)** → **Lab VMs (192.168.50.0/24)**

pfSense GUI: `https://192.168.50.1`

---

## pfSense version + interfaces

- pfSense: **2.8.1-RELEASE (amd64)**
- Hostname shown in console: `pfsense.home.arpa`

### Interface mapping

| Interface | Name | VirtualBox network | Addressing |
|---|---|---|---|
| `em0` | WAN | NAT | **DHCP** → `10.0.2.15/24` |
| `em1` | LAN | Host-Only (lab network) | **Static** → `192.168.50.1/24` |

**Screenshot placeholder:** `screenshots/01-pfsense-console-interfaces.png`  
*Capture:* pfSense console showing WAN/LAN interfaces + IPs (WAN `10.0.2.15`, LAN `192.168.50.1`)

---

## LAN network plan (192.168.50.0/24)

- Default gateway: `192.168.50.1` (pfSense LAN)
- Lab subnet: `192.168.50.0/24`
- DNS: (typically pfSense or your preferred DNS — document what you used if you changed it)

### Allocated IPs (DHCP leases / static mappings)

| VM / Hostname | IP |
|---|---|
| `kali` | `192.168.50.10` |
| `Ubuntu` | `192.168.50.20` |
| `WindowsVM` | `192.168.50.30` |
| `SuricataVM` | `192.168.50.40` |
| `WazuhServer` | `192.168.50.50` |

**Screenshot placeholder:** `screenshots/02-dhcp-leases.png`  
*Capture:* pfSense **Status → DHCP Leases** page showing the hosts above

---

## VirtualBox network settings (pfSense VM)

Configure pfSense with **two adapters**:

1. **Adapter 1 (WAN):** `NAT`  
2. **Adapter 2 (LAN):** `Host-Only Adapter` (example: `vboxnet0`)

All lab VMs should connect to the **same Host-Only network** as pfSense LAN.

**Screenshot placeholder:** `screenshots/03-virtualbox-adapters.png`  
*Capture:* VirtualBox settings page for pfSense showing Adapter 1 = NAT and Adapter 2 = Host-Only

---

## pfSense configuration steps (summary)

### 1) Assign interfaces
- WAN → `em0`
- LAN → `em1`

### 2) Set LAN IP
- LAN IPv4: `192.168.50.1/24`

### 3) Enable DHCP server on LAN
- Services → DHCP Server → LAN → Enable
- Configure the pool/range you want (document your chosen range here)

**Screenshot placeholder:** `screenshots/04-dhcp-server-lan-settings.png`  
*Capture:* Services → DHCP Server (LAN) showing enabled + range

### 4) NAT for internet access (default)
With WAN on VirtualBox NAT, pfSense typically works out-of-the-box using automatic outbound NAT.
