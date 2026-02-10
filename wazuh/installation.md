# Wazuh Server Installation (My Lab Setup)

This file documents **exactly what I set up** to get a working Wazuh Manager + Dashboard in my **VirtualBox mini-SOC lab**, using **pfSense as the gateway/DHCP**.

---

## Lab network (pfSense)

pfSense is my lab gateway and DHCP server.

- **LAN gateway:** `192.168.50.1/24`
- **WAN:** `10.0.2.15/24` (VirtualBox NAT)

### IPs I allocated (DHCP leases)
- `192.168.50.10` → `kali`
- `192.168.50.20` → `Ubuntu`
- `192.168.50.30` → `WindowsVM`
- `192.168.50.40` → `SuricataVM`
- `192.168.50.50` → `WazuhServer`

---

## Wazuh Server VM (VirtualBox)

I created a VM named **WazuhServer** in VirtualBox and connected it to the **pfSense LAN network** so it receives an IP from pfSense.

- **WazuhServer IP:** `192.168.50.50`
- **Gateway:** `192.168.50.1`

---

## Wazuh installation (All-in-one on WazuhServer)

On the WazuhServer VM, I installed the Wazuh stack as an **all-in-one** deployment (Dashboard + Manager + Indexer on the same machine).

### Commands I ran

```bash
sudo apt update && sudo apt -y upgrade
sudo apt -y install curl

curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```
At the end of the installation, the script printed:
- The Dashboard URL.
- The admin user credentials.
### Accessing the Wazuh Dashboard
From my lab network, I opened the Wazuh Dashboard in the browser using the WazuhServer IP:
- Dashboard URL: https://192.168.50.50
I logged in with the credentials generated during installation.
## Resault
At this point:
- pfSense routes the lab network (192.168.50.0/24)
- Wazuh is installed and reachable at https://192.168.50.50
- The Wazuh manager is ready to receive agents/logs (documented in agent-setup.md)
- Suricata integration is documented in the suricata/ folder (EVE JSON → Wazuh)
