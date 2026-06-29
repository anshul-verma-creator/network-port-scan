# Task 1: Local Network Port Scan using Nmap

## Objective
To perform basic network reconnaissance on a local network using Nmap, identify open ports and running services on connected devices, and analyze the potential security risks associated with this exposure.

## Tools Used
- **Nmap** v7.99 — for TCP SYN port scanning
- (Wireshark not used in this run — scan-only analysis)

## Methodology
1. Identified local IP address and subnet using `ipconfig`.
2. Determined the local network range: `10.217.xxx.0/24`.
3. Ran a TCP SYN scan against the subnet:
   ```
   nmap -sS 10.217.xxx.xx/24
   ```
4. Recorded open ports and associated services for each live host.
5. Researched each open service to assess potential security risk.

## Scan Results

```
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-29 21:25 +0530
Nmap scan report for 10.217.xxx.xxx
Host is up (0.014s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
53/tcp open  domain
MAC Address: 6E:3B:CE:6B:3B:A3 (Unknown)

Nmap scan report for 10.217.xxx.xx
Host is up (0.0000070s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3306/tcp open  mysql
5432/tcp open  postgresql

Nmap done: 256 IP addresses (2 hosts up) scanned in 10.24 seconds
```

## Findings Summary

| Host | Identity | Open Ports | Services |
|------|----------|-----------|----------|
| 10.217.xxx.xxx | Mobile phone (hotspot/gateway) | 53/tcp | DNS (domain) |
| 10.217.xxx.xxx | Scanning machine (localhost) | 135, 139, 445, 3306, 5432 | MSRPC, NetBIOS-SSN, SMB, MySQL, PostgreSQL |

Only **2 hosts** responded out of 256 addresses scanned in the subnet, indicating a small/private network (mobile hotspot with one connected device — the scanning machine itself).

## Service & Risk Analysis

| Port | Service | Description | Risk Level | Notes |
|------|---------|-------------|------------|-------|
| 53 | DNS (domain) | Resolves domain names to IP addresses | Low (internal) | Acting as the hotspot's gateway DNS resolver. Risk arises only if exposed publicly as an open recursive resolver (DNS amplification abuse). |
| 135 | MSRPC | Windows Remote Procedure Call endpoint mapper | Medium–High | Historically exploited by worms (e.g., Blaster, 2003). Should never be exposed to untrusted networks. |
| 139 | NetBIOS-SSN | Legacy NetBIOS session service for file/printer sharing | Medium | Often used alongside SMB; can leak hostname/workgroup info, used in older SMB relay attacks. |
| 445 | Microsoft-DS (SMB) | File and printer sharing protocol | High | Associated with critical exploits like EternalBlue (used in WannaCry, 2017). Should be firewalled from untrusted networks. |
| 3306 | MySQL | Relational database service | Medium–High | If accepting remote connections with weak/default credentials, allows unauthorized data access. Should bind to localhost unless intentionally shared. |
| 5432 | PostgreSQL | Relational database service | Medium–High | Same risk category as MySQL — restrict to local/trusted access only. |

## Key Observations
- The scanning machine (10.217.xxx.xx) exposes a notably large attack surface for a personal device — particularly the SMB (445) and database (3306, 5432) ports, which are commonly targeted in lateral movement and data exfiltration attacks.
- These services are likely required for local development work (e.g., running local databases), but they should ideally be:
  - Bound only to `localhost` (127.0.0.1) rather than all network interfaces, if external access isn't needed.
  - Protected by a firewall rule restricting access to trusted IPs only.
- No high-risk ports were found open on the hotspot/gateway device (10.217.xxx.xx) beyond standard DNS service.

## Recommendations
1. Disable or firewall SMB (139/445) if file sharing across the network isn't required.
2. Restrict MySQL/PostgreSQL to localhost-only connections unless remote access is explicitly needed.
3. Keep Windows OS and RPC services patched to mitigate known MSRPC-based exploits.
4. Periodically re-scan the network to monitor for unexpected new open ports/services.

## Conclusion
This exercise demonstrated basic network reconnaissance using Nmap's SYN scan technique. It highlighted how even a small, private network can expose multiple potentially risky services (SMB, MySQL, PostgreSQL) on a single host. Understanding this exposure is the first step toward hardening a device's network-facing attack surface.

---
*Task completed as part of internship cybersecurity training — Task 1: Network Port Scanning.*