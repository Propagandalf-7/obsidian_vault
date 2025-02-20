This document outlines the zone-based firewall rules as configured on our UniFi devices.

## General Principles
- **Inter-VLAN Isolation:** Default deny, allow only specific flows.
- **VPN-Only Access:** Critical zones (e.g., Infrastructure) are only accessible through VPN.
- **Least Privilege:** Only required ports and protocols are permitted.

## Example Rules

| Source Zone/VLAN                | Destination Zone/VLAN       | Allowed Ports | Conditions/Notes                                  |
|---------------------------------|-----------------------------|---------------|---------------------------------------------------|
| VPN (authenticated users)       | Infrastructure (VLAN 50)    | 22, 443, etc. | Only VPN IP range is allowed                      |
| Main (VLAN 10) / Mobile (VLAN 20) / Guest (VLAN 30) | Internet         | All outbound  | Block unsolicited inbound traffic               |
| Internal Services (VLAN 40)       | Other VLANs              | As required   | Application-specific rules apply                 |
| GitLab/CI-CD (VLAN 60)            | Internet                 | 80, 443       | Externally accessible with strict filtering       |
| Labs (VLAN 70)                  | Prod (VLAN 80)              | None          | Complete isolation between test and production    |

## Additional Notes
- **Logging and Monitoring:** Ensure all rules are logged and periodically reviewed.
- **Updates:** Update rules as network requirements change.
