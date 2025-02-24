---
title: Multicast
format:
  html:
    toc: true
    toc-depth: 2
    css: styles.css
editor_options:
  markdown:
    mode: gfm
---
#### Acronyms

- IGMP: Internet Group Management Protocol
- CGMP: Cisco Group Management Protocol
- MLD: Multicast Listener Discovery
- SPT: Shortest Path Tree
- ASM: Any-Source Multicast
- RP: Rendezvous Point
- PIM: Protocol Independent Multicast
  - PIM-DM: Dense Mode
  - PIM-SM: Spares Mode
  - BIDIR-PIM: Bidirectional Mode
- SSM: Source Specific Multicast
- RPF: Reverse Path Forwarding
- BSR: Bootstrap Router

---

#### Notes
Address Scope           Addresses                   Use

Local-scope             224.0.0.0/24                Reserved by the IANA for network protocol use, won't leave local network, TTL usually 1
Global-scope            224.0.1.0-238.255.255.255   Allocated dynamically throughout the Internet
Administratively scoped 239.0.0.0/8                 Reserved for use inside private domains

Address             Use
224.0.0.1           All Hosts
224.0.0.2           All IPv4 routers
224.0.0.13          All PIMv2 routers
224.0.0.5           Used by unicast routing protocols
224.0.0.6
224.0.0.9
224.0.0.10

### General
- Multicast is used to send the same data packets to multiple receivers
- Packets are duplicated by routers downstream of the source. Routers handle delivery to receivers
- Sender does not need to know the unicast address of receivers
- Receivers register for a multicast stream by telling their first-hop router
- RPF is used to ensure packets aren't looping
- Multicast is generally used for UDP based applications, this brings expected issues when network reliability is poor
- Multicast traffic is subject to eavesdropping as restricting who can access a stream is challenging
- Multicast uses include: IPTV, Training, Video Conferencing, Collaboration, Video on Demand
- Various models:
  - One-to-Many
  - Many-to-Many
  - Many-to-One
- Routers listen to multicast address ranges. Routing protocols manage groups
- Groups are managed by last hop router from source, communicates membership to the network
  - IGMP Messages are used by receivers to notify router when joining/leaving group
  - MLD messages used for IPv6
- PIM is a multicast routing protocol used to efficiently forward data
#### IGMP
- IGMP allows hosts to request to listen to a multicast stream from their upstream router. This then allows the router to join the requested multicast group and forward the traffic to the hosts network segment
- IGMPv3 is the current version of IGMP and is backwards compatible
- Initial Membership report is unsolicited, subsequent are in response to membership queries 
- membership queries and reports always have a TTL of one as they are only locally significant
- v2: Leave-group message: used by the host to inform the router they are leaving the group
- IGMPv3 is recent, still being implemented broadly. Major operating systems have support
- IGMPv3 adds 'Source Filtering' support. Receiver host sends a list of desired sources. Cisco IOS forwards traffic from only those sources
#### Source Trees vs Shared Trees
- Source trees are more efficient and used for low/single numbers of sources
- Shared trees are far more efficient as the number of sources increases
- (S,G) notation, This notation is essential in multicast routing because it defines a specific source (S) sending to a multicast group (G), preventing unwanted traffic from other sources
  - Source Trees: (S,G) : (source IP, Multicast IP)
  - Shared Trees: (S,G) : (*, Multicast IP)
- Rendezvous point is the Shared Root of a shared tree.
  - All source traffic must first go to the RP before being distributed to receivers
  - All receiver traffic will come from the RP, even if an upstream router or source has a shorter path to the receiver
- Distribution trees must be dynamic as multicast group members can join or leave any time
#### PIM
- PIM-SM is used universally over PIM-DM. PIM-DM is not appropriate for any production application
- PIM-DM Begins by flooding multicast traffic throughout the network. Routers with no downstream neighbors will then prune the unneeded traffic
- The State information is learned though this flooding and pruning process
- PIM-DM supports Source Trees, not Shared Trees
- PIM-SM uses an explicit pull mode. Traffic is specifically sent downstream when needed, not flooded
- PIM-SM uses an RP. The shared tree is rooted at the RP. Senders register with the RP and only sent one copy of traffic to it
- Group members are connected to the shared tree by the local DR(first-hop router?), by sending PIM join messages to an RP
- first-hop routers with directly connected traffic sources send PIM register messages to the RP.
- SPT Threshold can be configured to have more efficient pathing for high volume traffic. When the multicast traffic on a given flow exceeds this threshold, the last-hop router (the one directly connected to the multicast receivers) automatically joins the SPT. This means it signals its interest in receiving the multicast stream directly from the source rather than via the RP.
- Multiple RPs can be used for different groups or group ranges. Access-lists are used to control which groups use which RP
- PIM-SM Shared Tree Join
  - Receiver sends multicast IGMP membership report
  - DR on the same LAN receives the IGMP membership report
  - DR knows the RP routers IP address, and sends a (*,G) join where group is known from the Receivers IGMP membership report
  - (*,G) flows through the network to the RP, branch is created by this process so the tree includes a path from RP to Last-hop router
- PIM-SM Sender Registration
  - Source sends multicast packets
  - The First-Hop DR will  register that source with the RP by encapsulating the packets in PIM register messages and sending them tot he RP via unicast
  - This builds an SPT from the first-hop router to the RP so traffic can flow from the Source to the RP
  - When the RP is receiving multicast from the Source it generates a PIM register-stop message for the first-hop router which stops the unicast PIM register messages
  - This all allows multicast traffic to flow from the source to the RP and down to the receivers

- Designated Router and Last-Hop routers aren't always the same. 
  - DR sits on local LAN and represents that network in multicast routing protocols
  - Last-Hop Router is the last router before the receiver
- Auto-RP vs BSR

---

#### Commands
- `show ip mroute` - Displays the multicast routing table and helps verify the multicast tree structure
- `show ip pim neighbor` - Lists PIM neighbors to ensure proper PIM adjacencies
- `show ip igmp groups` - Confirms which multicast groups have active receivers on an interface
- `mtrace` - Traces the route of multicast packets to help pinpoint where packets are being dropped or mis-routed
