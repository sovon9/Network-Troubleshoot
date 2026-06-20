# Network-Troubleshoot
document on how to troubleshoot network related issues

# Network Troubleshooting Cheat Sheet (PPPoE Fiber)

## Network Topology

Normal Setup:

```text
ONU → Router → PC
```

Direct Test Setup:

```text
ONU → PC
```

---

# 1. Check Network Configuration

## Command

```cmd
ipconfig
```

## Purpose

Displays:

* IPv4 Address
* Subnet Mask
* Default Gateway
* DNS Servers

## Example

```text
IPv4 Address . . . . . . . . . . : 192.168.0.162
Default Gateway . . . . . . . . : 192.168.0.1
```

## Use Cases

* Verify PC received an IP address.
* Identify router IP (Default Gateway).

---

# 2. Test Router Connectivity

## Command

```cmd
ping -t 192.168.0.1
```

Replace with your router IP if different.

## Purpose

Tests:

```text
PC ↔ Router
```

## Healthy Result

```text
1–2 ms
0% packet loss
```

## Problem Indicators

| Symptom           | Possible Cause                       |
| ----------------- | ------------------------------------ |
| Request timed out | Bad cable, Wi-Fi issue, router issue |
| Large spikes      | Local network congestion             |
| Packet loss       | LAN problem                          |

---

# 3. Find ISP Gateway

## Command

```cmd
tracert 8.8.8.8
```

## Example

```text
Hop 1: 192.168.0.1
Hop 2: 103.43.80.106
```

The second hop is usually your ISP gateway.

---

# 4. Test ISP Gateway

## Command

```cmd
ping -n 100 <ISP_GATEWAY_IP>
```

Example:

```cmd
ping -n 100 103.43.80.106
```

## Purpose

Tests:

```text
PC → Router → ONU → ISP Gateway
```

## Healthy Result

```text
2–5 ms
0% packet loss
```

## Problem Indicators

| Symptom      | Possible Cause           |
| ------------ | ------------------------ |
| Packet loss  | Fiber issue              |
| High latency | ISP access network issue |
| Timeouts     | ONU or PPPoE issue       |

---

# 5. Test Internet Connectivity

## Google DNS

```cmd
ping -n 100 8.8.8.8
```

## Cloudflare DNS

```cmd
ping -n 100 1.1.1.1
```

## Purpose

Tests:

```text
Full Internet Path
```

## Healthy Result

```text
0% packet loss
Stable latency
```

## Problem Indicators

| Symptom      | Possible Cause         |
| ------------ | ---------------------- |
| Packet loss  | ISP routing issue      |
| Timeouts     | Upstream network issue |
| High jitter  | Congestion             |
| High latency | Routing problem        |

---

# 6. Continuous Monitoring

## Command

```cmd
ping -t 8.8.8.8
```

Stop with:

```text
Ctrl + C
```

## Purpose

Useful for:

* Detecting intermittent disconnects
* Monitoring gaming latency
* Checking video-call stability

---

# 7. Trace the Route

## Command

```cmd
tracert 8.8.8.8
```

## Purpose

Shows every router between you and the destination.

Example:

```text
PC
↓
Router
↓
ISP Gateway
↓
ISP Core Network
↓
Destination
```

## Use Cases

* Identify ISP gateway
* Detect unusually long routes

---

# 8. Packet Loss Analysis

## Command

```cmd
pathping 8.8.8.8
```

## Purpose

Combines:

```text
Ping + Traceroute
```

Provides:

* Packet loss by hop
* Latency by hop

## Interpretation

| Loss Starts At | Likely Cause      |
| -------------- | ----------------- |
| Router         | Local network     |
| ISP Gateway    | Fiber/PPPoE issue |
| Later hops     | ISP routing issue |

### Note

```text
* * *
```

on some hops is normal because many routers block ICMP responses.

---

# 9. Test DNS Resolution

## Command

```cmd
nslookup google.com
```

## Purpose

Tests DNS functionality.

## Healthy Result

Returns one or more IP addresses.

Example:

```text
Name: google.com
Addresses: 142.250.x.x
```

## Problem Indicators

| Symptom        | Meaning          |
| -------------- | ---------------- |
| DNS timeout    | DNS server issue |
| No IP returned | DNS problem      |

---

# 10. Direct ONU Test (Bypass Router)

## Purpose

Determines whether the router is causing issues.

## Physical Setup

```text
ONU → PC
```

---

## Create PPPoE Connection in Windows

Open:

```text
Control Panel
→ Network and Sharing Center
→ Set up a new connection or network
→ Connect to the Internet
→ Broadband (PPPoE)
```

Enter:

```text
ISP Username
ISP Password
```

Connect.

---

## Test

```cmd
ping -n 100 1.1.1.1
```

## Interpretation

| Result                 | Meaning                 |
| ---------------------- | ----------------------- |
| Packet loss remains    | ISP / ONU / Fiber issue |
| Packet loss disappears | Router issue            |

---

# Reading Packet Loss

Example Summary:

```text
Packets: Sent = 100
Received = 98
Lost = 2 (2% loss)
```

## Guidelines

| Loss % | Status        |
| ------ | ------------- |
| 0%     | Excellent     |
| 1–2%   | Warning       |
| 3–5%   | Problematic   |
| >5%    | Serious issue |

---

# Quick Diagnostic Flow

```text
1. Ping Router
   ↓
   Packet Loss?
      Yes → Local Network Problem
      No
       ↓

2. Ping ISP Gateway
   ↓
   Packet Loss?
      Yes → ONU/Fiber/PPPoE Problem
      No
       ↓

3. Ping 8.8.8.8 or 1.1.1.1
   ↓
   Packet Loss?
      Yes → ISP Routing/Upstream Problem
      No → Internet Healthy
```

---

# Commands Reference

## Network Configuration

```cmd
ipconfig
```

## Router Test

```cmd
ping -t 192.168.0.1
```

## ISP Gateway Test

```cmd
ping -n 100 <gateway-ip>
```

## Internet Test

```cmd
ping -n 100 8.8.8.8
```

```cmd
ping -n 100 1.1.1.1
```

## Continuous Monitoring

```cmd
ping -t 8.8.8.8
```

## Route Analysis

```cmd
tracert 8.8.8.8
```

## Packet Loss Analysis

```cmd
pathping 8.8.8.8
```

## DNS Test

```cmd
nslookup google.com
```
