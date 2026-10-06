WADF105 Practical Laboratory 2
OPNsense Firewall Policy Testing
---

This repository contains the laboratory report and evidence for Practical Laboratory 2: OPNsense Firewall Policy Testing, Logging and Packet Analysis.

## LAB OBJECTIVE

The laboratory demonstrates controlled firewall policy testing using an authorized OPNsense firewall and Ubuntu client. The work covers baseline connectivity, narrow firewall blocks, logging, packet capture, state inspection, and restoration of normal connectivity.

## LAB ENVIRONMENT

Firewall VM       : icdfa-nslab-firewall-v1
Client VM         : icdfa-nslab-client-v1
OPNsense LAN      : 10.10.10.1/24
Ubuntu client     : 10.10.10.237
Client interface  : enp0s3
Default gateway   : 10.10.10.1
WAN attachment    : VirtualBox NAT
Outbound NAT      : Automatic

## TEST RULES

1. LAB2 BLOCK ICMP TO 1.1.1.1
Action       : Block
IP version   : IPv4
Protocol     : ICMP
Source       : LAN net
Destination  : 1.1.1.1/32
Logging      : Enabled
2. LAB2 BLOCK OUTBOUND HTTP
Action          : Block
Protocol        : TCP
Source          : LAN net
Destination     : Any
Destination port: HTTP / 80
Logging         : Enabled

The specific block rules must be placed above the broad "Default allow LAN to any" rule because OPNsense processes interface rules from top to bottom.

## EVIDENCE INCLUDED

E1 - Successful baseline ping, DNS, HTTP and HTTPS tests.
E2 - OPNsense LAN rule evidence for the two LAB2 block rules.
E3 - Failed ICMP test with successful DNS/HTTPS evidence.
E4 - Failed HTTP test with successful HTTPS evidence.
E5 - OPNsense Live View evidence for blocked ICMP and TCP port 80 traffic.
E6 - Wireshark evidence for blocked ICMP/HTTP and permitted HTTPS traffic.
E7 - OPNsense state-table evidence for a permitted HTTPS connection.
E8 - Restored connectivity and final firewall state evidence.

## REPOSITORY FILES

README.md

WADF105\_Lab\_02\_Evidence\_Report.pdf


lab2\_evidence/

The PDF document is the main submission report and contains the evidence screenshots with captions.

