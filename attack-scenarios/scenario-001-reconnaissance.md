# Scenario 001 — Network Reconnaissance
 
## Engagement Context

As part of the Zaphod Bank SOC build, this scenario simulates the reconnaissance phase of an attack against Zaphod's internal corporate network. Once inside Zaphod's network, the attacker maps which ports and services are exposed to identify paths for lateral movement and further compromise. These ports were deliberately exposed to simulate an environment where network segmentation controls are immature, reflecting attack surface conditions documented in real-world financial sector breaches.

## MITRE ATT&CK Mapping
 
| Technique | ID | Tactic | Description |
|---|---|---|---|
| Active Scanning: Scanning IP Blocks | T1595.001 | Reconnaissance | Systematic port scanning to identify live hosts and exposed services |
| Active Scanning: Vulnerability Scanning | T1595.002 | Reconnaissance | Service version detection to identify potentially exploitable software |

## Environment
 
| Component | Details |
|---|---|
| Attacker machine | Kali Linux (192.168.0.118) |
| Target machine | Windows Server 2022 (192.168.0.29) |
| Network | Bridged (both machines on the same subnet, simulating internal network position post-compromise) |
| Tool used | Nmap 7.95 |

## Scenario Narrative
 
In this scenario the attacker has already gained an initial foothold on Zaphod's internal network, consistent with the assume breach posture that underpins Zaphod's detection strategy. From that position, the attacker performs active reconnaissance to identify which services are exposed on a target Windows Server, which informs their next actions.
 
This mirrors real-world attack patterns documented in the Verizon 2025 DBIR, where system intrusion techniques including internal reconnaissance accounted for 53% of financial sector breaches. Identifying exposed services such as RDP and SMB is a standard precursor to credential-based attacks and lateral movement.
 
