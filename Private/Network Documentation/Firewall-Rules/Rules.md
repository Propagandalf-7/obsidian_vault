# UniFi Zone-Based Firewall Rules

This document outlines our zone-based firewall rules for UniFi devices, focusing on **least-privilege access**, **inter-VLAN isolation**, and **secure service exposure**. Below is an updated rules table reflecting corrected zones, selective Guest→IoT access for casting, and a local DNS server for content filtering and internal DNS.

---

## 1. Overview

We separate the network into VLAN-based “zones,” each with its own security posture:

- **Default Deny:** Block all inter-zone traffic unless explicitly allowed.  
- **VPN-Only Access:** Infrastructure VLAN (management, backups, etc.) is only accessible via VPN.  
- **Selective Guest→IoT:** Guests can cast to specific IoT devices (e.g., Smart TV), but cannot freely access other IoT resources.  
- **Local DNS Server:** A dedicated DNS service (e.g., Pi-hole or AdGuard Home) provides content filtering for Main, Guest, and IoT VLANs, as well as custom DNS for internal services.

---

## 2. Network Diagram

```diagram
   +------------------------+
   | Main VLAN (10)        |----> Internet
   | (PCs, Laptops)        |       |
   +-----------+-----------+       |
               |                 ...
   +-----------v-----------+
   | Guest VLAN (30)       |----> Internet
   | (Visitors, etc.)      |       ^
   +-----------+-----------+       |
               |   (Cast to SmartTV)
   +-----------v-----------+
   | IoT VLAN (20)         |----> Internet (Limited Outbound)
   | (Smart TV, Devices)   |----> Local DNS for content filtering
   +-----------+-----------+
               |
               |   (VPN Only)
   +-----------v-----------+
   | Infrastructure (50)   |
   | (Proxmox, Backups,    |
   |  K8s control plane,   |
   |  local DNS server)    |
   +-----------+-----------+
               |
               v
   +------------------------+
   | Internal Services (40) |
   | (e.g., Jellyfin)       |
   +-----------+------------+
               |
               v
   +------------------------+
   | GitLab/CI-CD (60)      |
   | (via Cloudflare Tunnels)
   +-----------+------------+
               |
               v
   +------------------------+
   | Labs (70)             |
   | (Cloudflare Tunnels,  |
   |  no direct inbound)   |
   +-----------+------------+
               |
               v
   +------------------------+
   | Prod (80)             |
   | (Strict inbound only) |
   +------------------------+
```
Diagram Notes:

- Guest VLAN (30) has restricted access to IoT VLAN (20) only for casting to Smart TV.  
- Infrastructure VLAN (50) is off-limits except via VPN.  
- Local DNS server (e.g., Pi-hole/AdGuard) resides in Infrastructure VLAN (50) but services VLANs 10, 20, 30, etc.  
- GitLab/CI-CD (VLAN 60), Labs (70), and Prod (80) can be exposed via Cloudflare Tunnels or have minimal inbound firewall rules.  

---

## 3. Zone-to-VLAN Mapping

| Zone Name         | VLAN ID | Notes                                                    |
| ----------------- | ------- | -------------------------------------------------------- |
| Main              | 10      | Primary user devices (PCs, laptops).                     |
| IoT               | 20      | Smart TVs, smart home devices.                           |
| Guest             | 30      | Visitors, captive portal optional.                       |
| Internal Services | 40      | Local apps like Jellyfin.                                |
| Infrastructure    | 50      | Management, backups, hypervisors, local DNS, monitoring. |
| GitLab-CI/CD      | 60      | Self-hosted Git, CI/CD pipelines (via tunnels).          |
| Labs              | 70      | Testing/staging environment (via tunnels).               |
| Prod              | 80      | Production workloads with minimal inbound.               |

---

## 4. Firewall Rules Table

Below is a summarized firewall rules table. Adjust ports/services as needed.

| Source VLAN    | Destination VLAN             | Allowed Ports / Protocols            | Conditions / Notes                                                                                       |
| -------------- | ---------------------------- | ------------------------------------ | -------------------------------------------------------------------------------------------------------- |
| Main (10)      | Internet                     | 80/443 outbound                      | Standard user internet access. Enforce local DNS if using Pi-hole/AdGuard for content filtering.         |
| Main (10)      | IoT (20)                     | Optional (e.g., 8009 for Chromecast) | Allow only if certain Main devices must control or cast to IoT. Otherwise block all.                     |
| Main (10)      | Infrastructure (50)          | None (blocked)                       | Use VPN to access Infrastructure.                                                                        |
| IoT (20)       | Local DNS (50)               | 53 (DNS), possibly DoT/DoH           | Force IoT to use local DNS for content filtering.                                                        |
| IoT (20)       | Internet                     | 80/443 outbound only                 | Restrict to needed services (firmware updates, app connectivity). No inbound from Internet.              |
| Guest (30)     | Internet                     | 80/443 outbound                      | Captive portal optional. Force Guest VLAN to local DNS for content filtering if desired.                 |
| Guest (30)     | IoT (20)                     | Ports for casting (mDNS, SSDP, etc.) | Restricted to specific devices (e.g., Smart TV). Consider using firewall groups or ACLs for fine-tuning. |
| Guest (30)     | Main (10), Infra (50), etc.  | None (blocked)                       | No direct access unless specifically allowed.                                                            |
| Infr. (50)     | VPN                          | SSH (22), HTTPS (443), etc.          | Only accessible through UniFi VPN. No direct inbound from other VLANs.                                   |
| Int. Svcs (40) | LAN Clients (e.g., 10, etc.) | Varies by app (e.g., Jellyfin: 8096) | Accessible only on local subnets. No direct inbound from Internet unless reversed-proxied.               |
| Labs (60)      | Internet (Cloudflare)        | 80/443                               | No direct inbound. For demo/staging behind Cloudflare Tunnels.                                           |
| Prod (70)      | Internet                     | 80/443 or required ports             | Strict inbound only for essential services. Block all other traffic.                                     |

### Rule Explanations

- **Local DNS Server**  
  Typically located in Infrastructure VLAN (50) (e.g., Pi-hole/AdGuard).  
  VLANs 10 (Main), 20 (IoT), and 30 (Guest) forward DNS queries to this server for content filtering and custom internal DNS.

- **Guest → IoT Casting**  
  Limit to the Smart TV’s IP or relevant casting protocol ports (e.g., 8009 for Chromecast, 7000+ for AirPlay, mDNS on 5353, etc.).  
  Block all other IoT devices from Guest VLAN to prevent lateral movement.

- **VPN Access**  
  All Infrastructure resources require VPN.  
  No direct route from Main/IoT/Guest to Infrastructure except via VPN.

- **Cloudflare Tunnels**  
  Labs (60) are accessible externally through Cloudflare or similar reverse proxy.  
  Restrict inbound traffic to known Cloudflare IP ranges if possible.

- **Prod (70)**  
  Public-facing environment with strict, minimal inbound rules (e.g., HTTPS).  
  No direct inter-VLAN from Guest/IoT to Prod.

---

## 5. Additional Considerations

- **Logging & Monitoring**  
  Enable logging on all zone policies to detect unauthorized attempts.  
  Use centralized monitoring (Prometheus, Grafana, Wazuh, etc.) to track logs and alerts.

- **Auditing & Vulnerability Scanning**  
  Perform routine scans (Nmap, OpenVAS) from each VLAN to confirm isolation.  
  Integrate code and dependency scans (SonarQube, OWASP Dependency-Check) for self-hosted apps.

- **MAC Filtering**  
  Not a primary security measure; MAC addresses can be spoofed.  
  Use only as a supplement (e.g., blocking known rogue devices).

- **Documentation & Updates**  
  Maintain these rules under version control (Git).  
  Update the Network Diagram and Rules Table whenever a new device/VLAN/policy is introduced.

---

## 6. Revision & Maintenance

- **Change Management:** Keep a changelog of firewall policies.  
- **Quarterly Reviews:** Check logs, test connectivity, run scanning tools.  
- **DNS Updates:** Document custom records for internal services in a separate note or table (e.g., ../DNS/Local-Records.md).

---

## 7. References

- [UniFi Official Docs](https://help.ui.com/)
- [Cloudflare Tunnels](https://developers.cloudflare.com/cloudflare-one/)
- [Nmap](https://nmap.org/)
- [OpenVAS / Greenbone](https://www.greenbone.net/en/community-edition/)
- [Pi-hole](https://pi-hole.net/)
- [AdGuard Home](https://github.com/AdguardTeam/AdGuardHome)

Last Updated: 2025-02-20  
Maintainer: Homelab Admin