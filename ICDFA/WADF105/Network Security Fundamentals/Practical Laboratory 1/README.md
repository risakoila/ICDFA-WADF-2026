WADF105 Practical Laboratory 1
Two VM Network Commissioning and Connectivity Verification
---------------------------------------------------------------------------------------------

OVERVIEW
---------------------------------------------------------------------------------------------
This repository holds my evidence report for Practical Laboratory 1. The lab
builds a small protected network from two VirtualBox virtual machines, then
verifies addressing, routing, name resolution and internet access, and
observes ARP, ICMP and DNS traffic in Wireshark.

All work was done inside the authorized ICDFA laboratory environment.

CONTENTS
--------------------------------------------------------------------------------------------
README.txt
    This file.

WADF105_Lab_01_Evidence_Report.pdf
    The full report: summary of results, screenshots E1 to E7 with captions,
    answers to the five completion questions, and a troubleshooting appendix.


LAB ENVIRONMENT
--------------------------------------------------------------------------------------------
Hypervisor : Oracle VirtualBox
Firewall   : OPNsense 26.7 (amd64), VM "icdfa-nslab-firewall-v1"
Client     : Ubuntu desktop, VM "icdfa-nslab-client-v1"
Tools      : Wireshark, ip, resolvectl, getent, curl, ping


TOPOLOGY
--------------------------------------------------------------------------------------------

   Internet
      |
  VirtualBox NAT (gateway 10.0.2.2)
      |
  [em0 WAN 10.0.2.15/24, DHCP]
  +--------------------------+
  |   OPNsense firewall      |
  +--------------------------+
  [em1 LAN 10.10.10.1/24]
      |
  Internal Network "ICDFA-LAN"  (virtual switch)
      |
  [enp0s3 10.10.10.237/24, DHCP]
  +--------------------------+
  |   Ubuntu client          |
  +--------------------------+
  default gateway 10.10.10.1, DNS 10.10.10.1


ADDRESSING
--------------------------------------------------------------------------------------------
Firewall WAN (em0)       10.0.2.15/24 via DHCP, upstream gateway 10.0.2.2
Firewall LAN (em1)       10.10.10.1/24 static, DHCP range .100 to .200
Ubuntu client (enp0s3)   10.10.10.237/24 via DHCP
Firewall LAN MAC         08:00:27:48:41:99
Ubuntu client MAC        08:00:27:ff:8a:c6

The internal network name "ICDFA-LAN" is case-sensitive and must match on both
the firewall LAN adapter and the client adapter.


RESULTS
--------------------------------------------------------------------------------------------
Test  Command                         Result
1     ping -c 4 10.10.10.1            4/4 replies, 0% loss (gateway reachable)
2     ping -c 4 1.1.1.1               4/4 replies, 0% loss (routing and NAT work)
3     getent hosts opnsense.org       Address returned (DNS works)
4     curl -I https://opnsense.org    HTTP headers received (301 redirect)

Wireshark capture on enp0s3 showed:
  - ARP request "Who has 10.10.10.1?" and the reply giving the firewall MAC
  - ICMP echo request and reply pairs between the client and 10.10.10.1
  - DNS queries from the client to 10.10.10.1 and their responses


TROUBLESHOOTING NOTES
--------------------------------------------------------------------------------------------
1. Client had no IPv4 address. dhclient was not installed on the client, so
   the lease was renewed with: sudo systemctl restart NetworkManager

2. Ping to 1.1.1.1 returned "Destination Host Unreachable" from 10.10.10.1.
   The firewall routing table had no default route. Checking the table with
   "netstat -rn" and testing the WAN side (ping 10.0.2.2, TCP to 1.1.1.1:443,
   DNS lookup) showed the WAN link itself was healthy. Once the default route
   via 10.0.2.2 on em0 was present, internet access worked from both VMs.

3. Interface roles. VirtualBox Adapter 1 (NAT) and Adapter 2 (ICDFA-LAN) must
   be assigned to the matching OPNsense roles (WAN on the NAT adapter, LAN on
   ICDFA-LAN). Check this under Interfaces > Assignments or console option 1.

Useful checks: ip -br link, ip -4 -br address, ip route, resolvectl status,
netstat -rn (on the firewall shell).
