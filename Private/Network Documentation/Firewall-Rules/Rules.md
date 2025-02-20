
> **Diagram Notes:**  
> 1. Dashed or blocked lines indicate restricted or no direct traffic.  
> 2. Solid arrows to the Internet indicate outbound traffic allowed (usually on ports 80/443).  
> 3. Access to the Infrastructure VLAN (50) only occurs via VPN (not shown as a line here to emphasize it’s restricted).

(Place an actual image file, e.g. `Firewall-Zone-Diagram.png`, in your `Diagrams/` folder if you prefer a graphical representation. Update references accordingly.)

---

## 3. UniFi Zones and VLAN Mapping

Below are the zones defined in UniFi along with links to their respective documentation:

- **Main Zone (VLAN 10):** [[../VLANS/VLAN-10-main|Main (10)]]
- **IoT Zone (VLAN 20):** [[../VLANS/VLAN-20-iot|IoT (20)]]
- **Guest Zone (VLAN 30):** [[../VLANS/VLAN-30-guest|Guest (30)]]
- **Internal Services Zone (VLAN 40):** [[../VLANS/VLAN-40-internal-services|Internal Services (40)]]
- **Infrastructure Zone (VLAN 50):** [[../VLANS/VLAN-50-infrastructure|Infrastructure (50)]]
- **GitLab-CI/CD Zone (VLAN 60):** [[../VLANS/VLAN-60-gitlab-cicd|GitLab-CI/CD (60)]]
- **Labs Zone (VLAN 70):** [[../VLANS/VLAN-70-labs|Labs (70)]]
- **Prod Zone (VLAN 80):** [[../VLANS/VLAN-80-prod|Prod (80)]]

---

## 4. Firewall Rules Table

Below is an example table summarizing the primary rules enforced via our zone-based approach. Additional rules or refinements can be added as needed for specific services.

| Source Zone / VLAN           | Destination Zone / VLAN               | Allowed Ports / Protocols  | Conditions / Notes                                                                                 |
|------------------------------|----------------------------------------|----------------------------|-----------------------------------------------------------------------------------------------------|
| **Main (VLAN 10)**           | **Internet**                          | All outbound (80/443)      | - Default user network. No unsolicited inbound allowed.                                             |
| **IoT (VLAN 20)**            | **Internet**                          | 80/443 (outbound)          | - Isolated from Main.  <br/> - DNS only to local resolver if required.                              |
| **Guest (VLAN 30)**          | **Internet**                          | 80/443 (outbound)          | - Captive portal if desired. <br/> - No inter-VLAN access allowed.                                  |
| **Internal Services (40)**   | **LAN Clients** (e.g., VLAN 10)       | Varies by application      | - Local media servers (e.g., Jellyfin). <br/> - Allowed inbound from local VLANs only.             |
| **Infrastructure (50)**      | **VPN (UniFi)**                       | SSH (22), HTTPS (443), etc.| - Only accessible via an **authenticated** VPN connection. <br/> - No direct inbound from other VLANs.   |
| **GitLab-CI/CD (60)**        | **Internet (via Cloudflare Tunnels)** | 80/443                     | - Publicly accessible behind Cloudflare Tunnels. <br/> - Restrict to Cloudflare IP ranges.          |
| **Labs (70)**                | **Internet (via Cloudflare Tunnels)** | 80/443                     | - Test services can be externally accessible via tunnels only. <br/> - No direct inbound allowed.   |
| **Prod (80)**                | **Internet**                          | 80/443 (or required ports)  | - Production environment. <br/> - Strict inbound to essential services only.                        |

### Notes on Specific Rules

1. **Main (VLAN 10)**
   - General user devices. Internet access allowed, inbound blocked.
   - Cross-VLAN communication with IoT or Internal Services only if explicitly permitted.

2. **IoT (VLAN 20)**
   - Typically consumer IoT devices. Outbound HTTP/HTTPS permitted, inbound from Main or others is blocked unless needed.
   - DNS might be local (e.g., Pi-hole) or upstream, depending on your config.

3. **Guest (VLAN 30)**
   - Fully isolated from LAN. Captive portal optional.
   - Only outbound access to the Internet on 80/443.

4. **Internal Services (VLAN 40)**
   - E.g., Jellyfin, local media, or other internal-only applications.
   - Firewall rules permit local LAN clients (VLAN 10 or 20) if needed, but block external inbound unless used behind a proxy or tunnel.

5. **Infrastructure (VLAN 50)**
   - Houses Proxmox, Kubernetes control plane, backup servers, and monitoring.
   - **No direct access** except via VPN. Critical to keep this zone highly secure.

6. **GitLab-CI/CD (VLAN 60)**
   - Publicly accessible Git services through Cloudflare Tunnels or strict inbound firewall rules.
   - Integrates with Infrastructure for CI/CD but limit that access only to essential ports (e.g., SSH, API tokens, etc.).

7. **Labs (VLAN 70)**
   - Test environment for pre-production testing.
   - May also be exposed via Cloudflare Tunnels for demonstration or staging. No direct inbound.

8. **Prod (VLAN 80)**
   - Production environment for live services.
   - Minimal inbound rules, typically via standard web ports or specialized ports. No broad open inbound allowed.

---

## 5. Additional Considerations

1. **Logging & Monitoring**  
   - **Enable logging** on all deny/allow rules to help spot suspicious traffic.  
   - Use a centralized monitoring solution (e.g., Prometheus + Grafana, Wazuh, or Security Onion) to analyze logs.

2. **Auditing & Vulnerability Scanning**  
   - Periodically run [Nmap](https://nmap.org/) scans from different VLANs to verify isolation.  
   - Perform vulnerability scans with [OpenVAS/Greenbone](https://www.greenbone.net/en/community-edition/) to detect outdated services or misconfigurations.

3. **VPN Configuration**  
   - L2TP/IPsec (built into UniFi) secures remote access.  
   - Only allow management traffic to VLAN 50 (Infrastructure) from authenticated VPN clients.

4. **MAC Filtering**  
   - Not recommended as a primary security measure (MAC addresses can be spoofed).  
   - Use it only as an additional layer, not a replacement for proper firewall rules.

---

## 6. Revision & Maintenance

- **Update Frequency:**  
  Review this document each time you add or remove a service, change VLAN assignments, or modify the firewall.

- **Change Management:**  
  Use version control (e.g., Git) for your Obsidian vault to track these rules over time.

- **Review Schedule:**  
  Perform quarterly rule reviews to confirm that the configuration still meets your security and operational requirements.

---

## 7. References

- **UniFi Official Docs:** [Ubiquiti Help Center](https://help.ui.com/)  
- **Zone-Based Firewall Concepts:** [UniFi OS Documentation](https://www.ui.com/)  
- **Cloudflare Tunnels:** [Cloudflare Zero Trust Docs](https://developers.cloudflare.com/cloudflare-one/)  
- **Nmap:** [https://nmap.org/](https://nmap.org/)  
- **OpenVAS / Greenbone:** [https://www.greenbone.net/en/community-edition/](https://www.greenbone.net/en/community-edition/)

For VLAN-specific information, see the individual notes in the `../VLANs/` folder, and for IP addressing details, refer to `../IP-Addressing/Subnet-Plan.md`.

**Last Updated:** 2025-02-20  
**Maintainer:** _Homelab Admin_
