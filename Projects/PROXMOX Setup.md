## IP Adresses
proxmox: `http:\\192.168.8.200:8006`
jellyfin: `https:\\192.168.8.201:8096`

NAS: `\\192.168.8.200\NAS`

LCX Links: https://tteck.github.io/Proxmox/
## SETUP

### Setup PROXMOX
Link to NetworkChuck: https://www.youtube.com/watch?v=_u8qTN3cCnQ

1) Go to Datacentre
2) Go to Storage tab
3) Remove local-lvm
4) Go to proxmox node
5) Go to shell
6) Enter the following commands:
```bash
lvremove /dev/pve/data
```
```bash
lvresize -l 100%FREE /dev/pve/root
```
```bash
resize2fs /dev/mapper/pve/root
```
### Mount an external HDD

Identify disk: 
```bash
fdisk -l
```

Create a mount point:
```bash
mkdir /mnt/external_hdd
```

Mount the partition:
```bash
mount -t ntfs /dev/sdb1 /mnt/external_hdd
```

Setup auto mount:
Add the following line to the file `/etc/fstab`:
```bash
/dev/sdb1 /mnt/external_hdd ntfs defaults 0 0
```

### Share the drive over the network
Install SAMBA:
```bash
apt-get install samba
```

Replace the contents of `/etc/samba/smb/conf` with:
```text
[global]
   workgroup = WORKGROUP
   server string = Samba Server %v
   netbios name = your-server-name
   security = user
   map to guest = Bad User
   dns proxy = no

[NAS]
   comment = External HDD Share
   path = /mnt/external_hdd
   browseable = yes
   read only = no
   guest ok = yes
   create mask = 0755
   directory mask = 0755
```

Restart the SAMBA service:
`service smbd restart`

Add the network drive to windows.

### Setup Jellyfin server

LCX install:
```bash
  bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/ct/jellyfin.sh)"
```

