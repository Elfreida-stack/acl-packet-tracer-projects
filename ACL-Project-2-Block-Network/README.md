# ACL Project 2 — Block an Entire Network

## Overview

This project demonstrates how to use a Standard Access Control List (ACL) in Cisco Packet Tracer to block an entire network while allowing another network to communicate normally.

The network contains four PCs, two switches, one router, and one server.

## Objective

The objective of this project was to:

- Configure multiple networks using a Cisco router.
- Test connectivity before applying an ACL.
- Create a Standard ACL that blocks an entire network.
- Allow another network to continue communicating with the server.
- Verify that the ACL is working correctly.

## Network Topology

The topology contains:

- 4 PCs
- 2 Cisco 2960 switches
- 1 Cisco 2911 router
- 1 server

The networks used were:

- Network A: `192.168.10.0/24`
- Network B: `192.168.30.0/24`
- Server Network: `192.168.20.0/24`

## IP Addressing

| Device | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| PC0 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC1 | 192.168.10.20 | 255.255.255.0 | 192.168.10.1 |
| PC2 | 192.168.30.10 | 255.255.255.0 | 192.168.30.1 |
| PC3 | 192.168.30.20 | 255.255.255.0 | 192.168.30.1 |
| Server0 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |

### Router0 Interfaces

- G0/0 — `192.168.10.1/24`
- G0/1 — `192.168.30.1/24`
- G0/2 — `192.168.20.1/24`

## ACL Configuration

I created a Standard ACL to block the entire `192.168.10.0/24` network:

```text
access-list 20 deny 192.168.10.0 0.0.0.255
access-list 20 permit any
```

I then applied the ACL inbound on Router0's G0/0 interface:

```text
interface gigabitEthernet 0/0
ip access-group 20 in
```

## Testing Before the ACL

Before applying the ACL, connectivity was tested between the networks.

- PC0 successfully pinged Server0 with 0% packet loss.
- PC2 successfully pinged Server0 with 0% packet loss.

This confirmed that communication was working before the ACL was applied.

## Testing After the ACL

After applying ACL 20:

- PC0 → Server0: Destination host unreachable.
- PC1 → Server0: Destination host unreachable.
- PC2 → Server0: 0% packet loss.
- PC3 → Server0: 0% packet loss.

The results showed that the entire `192.168.10.0/24` network was blocked, while the `192.168.30.0/24` network was still able to communicate with the server.

## ACL Verification

I verified the ACL using:

```text
show access-lists
```

The router displayed:

```text
Standard IP access list 20
10 deny 192.168.10.0 0.0.0.255
20 permit any
```

The deny rule recorded **8 matches**, confirming that traffic from the blocked network was being denied.

I also verified the interface configuration using:

```text
show ip interface gigabitEthernet 0/0
```

The output confirmed that **ACL 20 was applied inbound** on GigabitEthernet0/0.

## What I Learned

This project helped me understand the difference between blocking one specific device and blocking an entire network.

In my first ACL project, I used a Standard ACL to block one specific host. In this project, I used a network address and wildcard mask to block every device belonging to the `192.168.10.0/24` network.

I also learned that creating an ACL is not enough. The ACL must be applied to the correct router interface and in the correct direction before it can filter traffic.

## Skills Demonstrated

- Cisco Packet Tracer
- Standard ACL configuration
- Network access control
- Wildcard masks
- Permit and deny rules
- Interface ACL application
- Network connectivity testing
- Cisco IOS commands
- ACL verification
- Basic network troubleshooting

## Project Files

- `ACL-Project-2-Block-Network.pkt`
- Screenshots documenting the topology, connectivity tests, ACL configuration, and verification
