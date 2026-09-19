# ACL Project 3 — Extended ACL: Control Specific Traffic

## Overview

This project demonstrates how to use an Extended Access Control List (ACL) in Cisco Packet Tracer to control specific types of network traffic.

Unlike a Standard ACL, which mainly filters traffic based on the source IP address, an Extended ACL can make more specific decisions using the source IP, destination IP, protocol, and port number.

In this project, I configured an Extended ACL to block FTP traffic from one specific PC to a specific server while still allowing other types of traffic such as ping and HTTP.

## Objective

The objectives of this project were to:

- Build and configure a small routed network.
- Configure IP addressing on PCs, a server, and a Cisco router.
- Enable HTTP and FTP services on the server.
- Test network connectivity before applying an ACL.
- Create an Extended ACL to block specific FTP traffic.
- Allow other traffic from the same PC to continue working.
- Allow another PC to continue using FTP.
- Verify the ACL configuration and match counts.

## Network Topology

The topology contains:

- 3 PCs
- 2 Cisco 2960 switches
- 1 Cisco 2911 router
- 1 server

The network uses three different IP networks:

- PC Network: `192.168.10.0/24`
- Second PC Network: `192.168.30.0/24`
- Server Network: `192.168.20.0/24`

## IP Addressing

| Device | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| PC0 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC1 | 192.168.10.20 | 255.255.255.0 | 192.168.10.1 |
| PC2 | 192.168.30.10 | 255.255.255.0 | 192.168.30.1 |
| Server0 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |

### Router0 Interfaces

- G0/0 — `192.168.10.1/24`
- G0/1 — `192.168.30.1/24`
- G0/2 — `192.168.20.1/24`

All three router interfaces were configured and brought up using `no shutdown`.

## Server Services

Server0 was configured with:

- HTTP service enabled
- FTP service enabled

An FTP account was created for testing:

- Username: `testuser`
- Password: `test123`
- Permission: Read

## Testing Before the ACL

Before applying the Extended ACL, I tested the network to confirm that the required traffic was working.

### PC0 Tests

- PC0 successfully pinged Server0.
- PC0 successfully accessed the Server0 HTTP webpage.
- PC0 successfully connected to Server0 using FTP.

### PC1 Test

- PC1 successfully connected to Server0 using FTP.

These tests confirmed that the network and services were working before the ACL was applied.

## Extended ACL Configuration

I created Extended ACL 100 to block FTP traffic from PC0 to Server0.

The ACL configuration was:

```text
access-list 100 deny tcp host 192.168.10.10 host 192.168.20.10 eq 21
access-list 100 permit ip any any
```

The first rule blocks TCP traffic from PC0 to Server0 on port 21, which is used for FTP control traffic.

The second rule allows all other IP traffic.

The ACL was then applied inbound on Router0's G0/0 interface:

```text
interface gigabitEthernet 0/0
ip access-group 100 in
```

## Testing After the ACL

After applying ACL 100, I tested the same traffic again.

### PC0 Results

- PC0 → Server0 FTP: **Blocked**
- PC0 → Server0 Ping: **Allowed**
- PC0 → Server0 HTTP: **Allowed**

When PC0 attempted to connect to FTP, the connection timed out and displayed an FTP error.

The successful ping and HTTP connection showed that the ACL was not blocking PC0 completely. It was specifically filtering the FTP traffic.

### PC1 Result

PC1 was still able to connect to Server0 using FTP.

This demonstrated that the ACL was specific to PC0's FTP traffic rather than blocking FTP traffic from the entire network.

## ACL Verification

I used the following command to verify ACL 100:

```text
show access-lists
```

The router showed:

```text
Extended IP access list 100
10 deny tcp host 192.168.10.10 host 192.168.20.10 eq FTP
20 permit ip any any
```

The deny rule recorded **24 matches**, while the permit rule recorded **16 matches**.

The match counts provided evidence that the ACL was actively processing and filtering traffic.

I also verified the ACL's placement using:

```text
show ip interface gigabitEthernet 0/0
```

The output confirmed:

**Inbound access list is 100**

This verified that ACL 100 was applied inbound on Router0's G0/0 interface.

## What I Learned

This project helped me understand the main difference between Standard and Extended ACLs.

A Standard ACL can make decisions mainly based on the source IP address, while an Extended ACL provides much more specific control.

With an Extended ACL, I was able to specify:

- The source device
- The destination server
- The transport protocol
- The destination port

This allowed me to block only FTP traffic from PC0 to Server0 without blocking PC0 from using ping or HTTP.

I also learned why ACL placement and direction are important. The ACL had to be applied to the correct interface and in the correct direction for the traffic to be filtered as intended.

## Skills Demonstrated

- Cisco Packet Tracer
- Extended ACL configuration
- TCP traffic filtering
- FTP traffic control
- HTTP testing
- IP addressing
- Cisco IOS commands
- ACL match-count verification
- Interface ACL application
- Network connectivity testing
- Basic network troubleshooting

## Project Files

- `ACL-Project-3-Extended-ACL.pkt`
- `Screenshots/`

The screenshots document the network configuration, server services, connectivity tests, ACL configuration, blocked FTP traffic, successful allowed traffic, and ACL verification.
