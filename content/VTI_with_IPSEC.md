---
title: VTI with IPSEC
format:
  html:
    toc: true
    toc-depth: 2
editor_options:
  markdown:
    mode: gfm
---

#### Acronyms

- VTI: Virtual Tunnel Interface
- IPsec: Internet Protocol Security
- ISAKMP: Internet Security Association and Key Management Protocol
- IKE: Internet Key Exchange
- ESP: Encapsulating Security Payload
- AH: Authentication Header
- SA: Security Association
- SPI: Security Parameter Index

---

#### Notes

- The tunnel interface acts like a point-to-point link.
- Traffic routed through Tunnel0 is automatically encrypted with IPsec.
- Routing protocols (e.g., OSPF, BGP) can be enabled directly on Tunnel0.
	
---

#### Commands
- `show crypto isakmp sa` - Displays the ISAKMP Security Association
- `show crypto ipsec sa` - Displays the IPsec Security Association
- `show run | section crypto` - Shows crypto related configurations

---

#### Config

##### Create a Virtual Tunnel Interface

```
interface Tunnel0
 ip address 192.168.1.1 255.255.255.252
 ! can also use 'ip unnumbered <interface>'
 tunnel source GigabitEthernet0/0
 tunnel destination 203.0.113.2
 tunnel mode ipsec ipv4
```

---

##### Define the IPsec Profile

```
crypto isakmp policy 10
 encryption aes 256
 hash sha256
 authentication pre-share
 group 14
 lifetime 86400
```

---

##### Define the IPsec Pre-Shared Key (as that was our chosen authentication method in the IPsec Profile)

```
crypto isakmp key MY_SECRET_KEY address 203.0.113.2
```

---

##### Define the IPsec Transform-Set

```
crypto ipsec transform-set MY_TRANSFORM_SET esp-aes 256 esp-sha-hmac
 mode tunnel
```

---

##### Apply the IPsec Profile to the Tunnel

```
interface Tunnel0
 tunnel protection ipsec profile MY_IPSEC_PROFILE
```

```
code
```