We use RFC1918 addresses from the 10.0.0.0/16 range. The plan is to align VLAN IDs with the subnet numbering for ease of management.

| VLAN Name              | VLAN ID | Subnet          |
|------------------------|---------|-----------------|
| Main                   | 10      | 10.0.10.0/24    |
| Mobile                 | 20      | 10.0.20.0/24    |
| Guest                  | 30      | 10.0.30.0/24    |
| Internal Services      | 40      | 10.0.40.0/24    |
| Infrastructure         | 50      | 10.0.50.0/24    |
| GitLab/CI-CD           | 60      | 10.0.60.0/24    |
| Labs                   | 70      | 10.0.70.0/24    |
| Prod                   | 80      | 10.0.80.0/24    |

> **Note:**  
> Leave gaps for future expansion if needed.

## Detailed VLAN Documentation
- [[../VLANS/VLAN-10-main|Main VLAN (10)]]
- [[../VLANS/VLAN-20-IoT|IoT VLAN (20)]]
- [[../VLANS/VLAN-30-guest|Guest VLAN (30)]]
- [[../VLANS/VLAN-40-internal-services|Internal Services VLAN (40)]]
- [[../VLANS/VLAN-50-infrastructure|Infrastructure VLAN (50)]]
- [[../VLANS/VLAN-60-gitlab-cicd|GitLab/CI-CD VLAN (60)]]
- [[../VLANS/VLAN-70-labs|Labs VLAN (70)]]
- [[../VLANS/VLAN-80-prod|Prod VLAN (80)]]
