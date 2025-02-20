**Purpose:**  
Hosts self-hosted Git services (e.g., GitLab) which are also accessible from the Internet with controlled filtering.

**VLAN ID:** 60  
**Subnet:** 10.0.60.0/24

## Devices and Services
- GitLab server for code hosting and CI/CD pipelines.

## Firewall Considerations
- Externally accessible on ports 80/443.
- Ensure proper security hardening and regular vulnerability scanning.
- For subnet details refer to [[../IP-Addressing/Subnet-plan|Subnet Plan]]
