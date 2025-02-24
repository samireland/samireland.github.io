---
title: AWS Direct Connect
format:
  html:
    toc: true
    toc-depth: 2
editor_options:
  markdown:
    mode: gfm
---
#### Acronyms

- DXCON: Direct Connect
- VIF: Virtual Interface
- VGW: Virtual Private Gateway
  - Used to terminate VPNs and VIFs within a VPC
- 

---

---

#### Notes

- Types of VIF
  - Public VIF
  - Private VIF
  - Transit VIF
- Limitations
  - 51 total VIFs max per DXCON
  - 4 Transit VIFs max per DXCON
- Public VIFs are used to access AWS public services that would otherwise be accessed over the internet. They allow access to all AWS regions via whatever region you connect to
- Private VIFs are used to access specific VPCs that may belong to your organisation that you don't want internet facing
- VPCs in other AWS regions are not accessible via a private VIF terminating on a VGW
- 
	
---

#### Commands
- `show command 1` - What the command does / When its useful
- `show command 2` - What the command does / When its useful
- `show command 3` - What the command does / When its useful





---

#### Config

---

##### Create a XYZ
```
interface Tunnel0
 ip address 192.168.1.1 255.255.255.252 #or ip unnumbered <interface>
 tunnel source GigabitEthernet0/0
 tunnel destination 203.0.113.2
 tunnel mode ipsec ipv4
```

---

##### Define the XYZ
```
crypto isakmp policy 10
 encryption aes 256
 hash sha256
 authentication pre-share
 group 14
 lifetime 86400
```