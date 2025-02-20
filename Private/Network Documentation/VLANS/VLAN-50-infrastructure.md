**Purpose:**  
Contains management interfaces, monitoring systems, backups, and Proxmox clusters.

**VLAN ID:** 50  
**Subnet:** 10.0.50.0/24

## Devices and Services
- Proxmox management nodes.
- Kubernetes control nodes.
- Monitoring servers.
- Backup systems.

## Firewall/VPN Considerations
- **Access:** Only accessible via VPN.
- **Rules:** Strict firewall rules block any direct inter-VLAN access from less-trusted zones.
- For subnet details refer to [[../IP-Addressing/Subnet-plan|Subnet Plan]]
