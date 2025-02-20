To ensure that our firewall rules and VLAN segmentation are working as expected, we regularly perform network scans.

## Tools
- **Nmap:**  
  For detailed port and service scans.
- **ZMap:**  
  For fast, large-scale scanning if required.

## Process
- Perform scans from multiple VLANs to verify that only allowed ports are accessible.
- Document scan results and compare against expected configurations.

## Example Command
```bash
nmap -sS -p 22,80,443 10.0.50.0/24
```

