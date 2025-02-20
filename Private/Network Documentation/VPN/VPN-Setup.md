
We use UniFi’s built-in VPN functionality (typically L2TP/IPsec) to secure remote access to the Infrastructure VLAN (VLAN 50).

## Configuration Overview
- **VPN Server:** Configured on the UniFi Security Gateway or Dream Machine.
- **Protocol:** L2TP/IPsec.
- **Clients:** Use native VPN clients on Windows, macOS, or Android. (There is no dedicated UniFi VPN client.)

## Access Control
- **VPN-Only Policy:**  
  Firewall rules enforce that only devices connected via VPN can access critical management interfaces in VLAN 50.
- **Authentication:**  
  Use strong passwords and consider certificate-based authentication or multi-factor authentication if supported.

## Testing
- Regularly test the VPN from remote networks to ensure that only authenticated users gain access.
