# ACL Project 4 — ACL Troubleshooting

##  Project Overview

This project focuses on troubleshooting and correcting an Extended Access Control List (ACL) in Cisco Packet Tracer.

Instead of simply creating an ACL that works, I intentionally worked with a faulty ACL configuration and used troubleshooting commands and connectivity tests to identify why the expected traffic was not being blocked.

The project demonstrates how ACL configuration errors can affect network traffic and how to verify, troubleshoot, and correct them.

---

##  Project Objectives

- Build a small routed network in Cisco Packet Tracer.
- Configure IPv4 addressing on PCs, a server, and a router.
- Establish baseline connectivity before applying an ACL.
- Create an intentionally faulty Extended ACL.
- Test connectivity to identify unexpected behavior.
- Use Cisco IOS troubleshooting commands to investigate the ACL.
- Identify an incorrect IP address in the ACL.
- Correct the ACL configuration.
- Verify that the intended traffic is blocked while permitted traffic continues to work.
- Verify ACL match counts.

---

##  Network Topology

The network contains:

- 1 Cisco 2911 Router
- 1 Cisco 2960 Switch
- 2 PCs
- 1 Server

### Network Layout

```text
PC0 ─────┐
         │
PC1 ─────┤── Switch0 ─── Router0 ─── Server0
         │
         └───────────────
```

---

##  IP Addressing

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| PC0 | FastEthernet | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC1 | FastEthernet | 192.168.10.20 | 255.255.255.0 | 192.168.10.1 |
| Router0 | G0/0 | 192.168.10.1 | 255.255.255.0 | — |
| Router0 | G0/1 | 192.168.20.1 | 255.255.255.0 | — |
| Server0 | FastEthernet | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |

---

##  Initial Router Configuration

Router0 was configured with two interfaces:

```text
interface gigabitEthernet 0/0
ip address 192.168.10.1 255.255.255.0
no shutdown

interface gigabitEthernet 0/1
ip address 192.168.20.1 255.255.255.0
no shutdown
```

The interfaces were verified using:

```text
show ip interface brief
```

Both interfaces were confirmed to be up/up.

---

##  Baseline Connectivity Test

Before applying the ACL, connectivity between the devices was tested.

### PC0 → Server0

```text
ping 192.168.20.10
```

Result:

**0% packet loss**

### PC1 → Server0

```text
ping 192.168.20.10
```

Result:

**0% packet loss**

This confirmed that both PCs could communicate with the server before the ACL was applied.

---

##  Troubleshooting Scenario

An Extended ACL was introduced to block traffic from PC1 to Server0 while allowing other IP traffic.

The intended rule was:

```text
access-list 110 deny ip host 192.168.10.20 host 192.168.20.10
```

However, an incorrect source IP was initially entered:

```text
access-list 110 deny ip host 192.169.10.20 host 192.168.20.10
```

The problem was the typo:

- Incorrect: `192.169.10.20`
- Correct: `192.168.10.20`

Because the ACL was looking for the wrong source IP, PC1's actual traffic did not match the deny rule and was allowed through by the following:

```text
access-list 110 permit ip any any
```

As a result, PC1 was initially able to ping Server0 with **0% packet loss**.

---

##  Troubleshooting Process

Several Cisco IOS commands were used to investigate the problem.

### 1. Check the ACL

```text
show access-lists
```

This displayed the ACL rules and their match counts.

### 2. Verify the ACL on the interface

```text
show ip interface gigabitEthernet 0/0
```

The output confirmed:

```text
Inbound access list is 110
```

This showed that ACL 110 was correctly applied inbound on GigabitEthernet 0/0.

### 3. Examine ACL Match Counts

The ACL initially showed traffic being processed by the permit rule instead of the intended deny rule.

This helped identify that the deny rule was not matching PC1's actual source address.

### 4. Identify the IP Address Error

The incorrect source address:

```text
192.169.10.20
```

was compared with PC1's actual address:

```text
192.168.10.20
```

The incorrect `169` instead of `168` was the cause of the problem.

---

##  Corrected ACL Configuration

The incorrect ACL was removed and recreated with the correct source address:

```text
configure terminal
no access-list 110
access-list 110 deny ip host 192.168.10.20 host 192.168.20.10
access-list 110 permit ip any any
end
```

The ACL was applied inbound on GigabitEthernet 0/0:

```text
interface gigabitEthernet 0/0
ip access-group 110 in
```

---

##  Final ACL

The final ACL configuration was:

```text
Extended IP access list 110
    10 deny ip host 192.168.10.20 host 192.168.20.10
    20 permit ip any any
```

The ACL was applied inbound on:

```text
GigabitEthernet 0/0
```

---

##  Final Verification

### PC1 → Server0

```text
ping 192.168.20.10
```

Result:

**Destination host unreachable**

This confirmed that the ACL successfully blocked PC1 from reaching Server0.

### PC0 → Server0

text
ping 192.168.20.10


Result:

0% packet loss

This confirmed that PC0 was still allowed to communicate with Server0.



##  Final ACL Match Counts

The final verification showed:

text
Extended IP access list 110
    10 deny ip host 192.168.10.20 host 192.168.20.10 (4 matches)
    20 permit ip any any (4 matches)


The deny rule having **4 matches** confirmed that traffic from PC1 to Server0 was actually being caught by the ACL.



##  What I Learned

This project helped me understand that troubleshooting an ACL is not just about checking whether the ACL exists.

I learned to:

 Check the actual ACL rules.
 Verify which interface the ACL is applied to.
 Check whether the ACL is applied inbound or outbound.
 Use ACL match counts to determine whether traffic is hitting a specific rule.
 Compare ACL IP addresses with the actual device IP addresses.
 Recognize how a small IP address typo can completely change the behavior of an ACL.
 Test both blocked and permitted traffic after making a correction.

One of the biggest lessons from this project was that **an ACL can look correctly configured at first glance but still fail to produce the expected result because of a small addressing mistake.**



##  Skills Demonstrated

 Cisco Packet Tracer
 Extended ACLs
 IPv4 Addressing
 Cisco IOS CLI
 ACL Troubleshooting
 Network Connectivity Testing
 Ping Testing
 ACL Verification
 Interface Verification
 Traffic Filtering
 Troubleshooting IP Addressing Errors



## Screenshots

The `Screenshots` folder contains evidence of the project, including:

1. Network topology
2. PC and server IP configurations
3. Router interface configuration
4. Baseline connectivity tests
5. Faulty ACL configuration
6. ACL troubleshooting
7. Corrected ACL configuration
8. PC1 blocked after correction
9. PC0 remaining accessible
10. Final ACL verification and match counts

---

## Project Files

```text
ACL-Project-4-ACL-Troubleshooting/
│
├── ACL-Project-4-ACL-Troubleshooting.pkt
│
└── Screenshots/
    ├── Project screenshots
    └── ACL verification evidence
```

---

##  Project Summary

This project demonstrated the complete troubleshooting process for an Extended ACL in Cisco Packet Tracer.

The initial configuration contained an incorrect source IP address, which prevented the deny rule from matching the intended traffic. By testing connectivity, checking ACL match counts, verifying the interface configuration, and comparing the ACL with the actual network addressing, the error was identified and corrected.

After the correction, PC1 was successfully blocked from reaching Server0, while PC0 continued to communicate with the server normally.

**Project Status: Completed ✅**
