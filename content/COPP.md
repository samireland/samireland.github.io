---
title: Control Plane Policing
format:
  html:
    toc: true
    toc-depth: 2
editor_options:
  markdown:
    mode: gfm
---
#### Acronyms

- Acronym1: what it stands for
- Acronym2: what it stands for

---

#### Notes

- Note1
- Note2
- Note3
	
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

