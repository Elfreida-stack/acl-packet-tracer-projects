# ACL Project 1 — Block One Specific Device

## Overview

This project demonstrates how to configure and test a Standard Access Control List (ACL) in Cisco Packet Tracer.

The goal was to block one specific PC from communicating with a server while allowing another PC on the same network to communicate with the server.

This project helped me understand how ACLs can be used to control network traffic based on source IP addresses.

## Objective

The objective of this lab was to:

- Configure a small routed network.
- Configure IP addresses on PCs, a router, and a server.
- Verify connectivity before applying an ACL.
- Create a Standard ACL.
- Block one specific host using its IP address.
- Allow other hosts to communicate normally.
- Apply the ACL to a router interface.
- Verify that the ACL was working correctly.

## Network Topology

The network consists of:

- 2 PCs
- 1 Cisco 2960 switch
- 1 Cisco 2911 router
- 1 server

The PCs are connected to the switch, the switch is connected to the router, and the router is connected to the server.

## IP Addressing

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| PC0 | FastEthernet0 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC1 | FastEthernet0 | 192.168.10.20 | 255.255.255.0 | 192.168.10.1 |
| Router | GigabitEthernet0/0 | 192.168.10.1 | 255.255.255.0 | — |
| Router | GigabitEthernet0/1 | 192.168.20.1 | 255.255.255.0 | — |
| Server0 | FastEthernet0 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |

## ACL Configuration

A Standard ACL numbered 10 was created to block PC0 while allowing all other traffic.

```text
access-list 10 deny host 192.168.10.10
access-list 10 permit any
```

The ACL was then applied inbound to the router's GigabitEthernet0/0 interface:

```text
interface gigabitEthernet 0/0
ip access-group 10 in
```

## Testing

### Before Applying the ACL

Before applying the ACL, both PCs were able to communicate with the server.

**PC0 → Server**

- Sent: 4
- Received: 4
- Lost: 0
- Packet loss: 0%

**PC1 → Server**

- Sent: 4
- Received: 4
- Lost: 0
- Packet loss: 0%

This confirmed that the network was working before traffic filtering was introduced.

### After Applying the ACL

After applying ACL 10:

**PC0 → Server**

- Traffic was blocked.
- PC0 received "Destination host unreachable."

**PC1 → Server**

- Sent: 4
- Received: 4
- Lost: 0
- Packet loss: 0%

This confirmed that the ACL blocked the intended device without blocking PC1.

## ACL Verification

The `show access-lists` command was used to verify the ACL.

The ACL showed:

```text
Standard IP access list 10
10 deny host 192.168.10.10
20 permit any
```

The deny rule recorded 4 matches from PC0's four ping attempts.

The permit rule recorded 4 matches from PC1's four ping attempts.

The `show ip interface gigabitEthernet 0/0` command also confirmed:

```text
Inbound access list is 10
```

This verified that ACL 10 was applied inbound on GigabitEthernet0/0.

## What I Learned

From this project, I learned how a Standard ACL can be used to control network access based on a source IP address.

I also learned that an ACL needs to be applied to an interface before it can actually filter traffic. Testing connectivity before and after applying the ACL helped me understand the effect of the configuration instead of only entering commands without verifying the results.

I also practiced using commands such as:

- `show access-lists`
- `show ip interface`
- `ping`
- `ip access-group`

## Skills Demonstrated

- Cisco Packet Tracer
- IPv4 addressing
- Basic router configuration
- Network connectivity testing
- Standard ACL configuration
- Source IP filtering
- ACL verification
- Network troubleshooting
- Command-line interface (CLI)

## Project Files

- Packet Tracer topology: `ACL-Project-1-Block-One-Device.pkt`
- Screenshots: See the uploaded project screenshots
