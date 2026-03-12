# Environment Architecture

## Overview

This document describes the technical environment built to simulate
Zaphod Bank's internal corporate infrastructure. Every component
was selected based on its relevance to the UK financial services
threat landscape and its direct applicability to the attack
techniques and detection scenarios documented in this engagement.

The environment deliberately replicates conditions found in real
UK enterprise environments: a Windows-based internal network
exposed to an external threat actor, monitored by a cloud-native
SIEM. This reflects the post-compromise internal network position
an attacker would occupy following initial access to Zaphod's
infrastructure.

---

## Platform Decision: Why UTM on Apple Silicon

The lab runs on an Apple MacBook Pro M4 using UTM as the
virtualisation platform. UTM was selected because it is built
specifically for Apple Silicon's ARM architecture, which
VirtualBox does not support. Using Apple's native Hypervisor
framework, UTM delivers stable performance and full
compatibility on M-series chips without the instability and
performance degradation that affects x86-based virtualisation
tools running under emulation.

Running the environment locally rather than in the cloud avoids
ongoing infrastructure costs, making it practical for a sustained
engagement. Microsoft Sentinel runs on Azure free tier, meaning
the environment deliberately combines local virtualisation with
cloud-based SIEM. This reflects the hybrid architecture that
characterises real enterprise environments where on-premise
infrastructure feeds into cloud security monitoring platforms.

---

## Component Architecture

### Kali Linux: External Threat Actor

Kali Linux represents the external threat actor targeting
Zaphod Bank's infrastructure. It is the industry standard
platform for offensive security operations, used by red teams
and penetration testers globally. Rather than installing
individual security tools, Kali comes pre-equipped with
the full toolset required for this engagement: Nmap for
network reconnaissance, Hydra for credential attacks, and
Burp Suite for web application testing.

In Zaphod's context, Kali simulates a financially motivated
criminal threat actor who has obtained a foothold on the
network, consistent with the post-compromise lateral movement
scenarios documented in the MITRE ATT&CK framework.

### Windows Server 2022: Target Environment

Windows Server 2022 represents Zaphod Bank's internal corporate
infrastructure. It was selected because it is the version UK
enterprises are actively migrating to from end-of-life
environments. Windows Server 2012 reached end of support in
October 2023, and Windows Server 2016 mainstream support ended
in January 2022(with extended support ending in January 2027).
Windows Server 2025, whilst released in
November 2024, has not yet reached widespread enterprise
adoption, as organisations typically take several years to
assess compatibility and plan migrations following a new release.
Building detections on Windows Server 2022 ensures direct
applicability to the environments most commonly encountered
in UK financial services organisations today.

The following services are deliberately exposed on this host
to simulate a realistic corporate server attack surface:

| Port | Protocol | Service | Attack Relevance |
|------|----------|---------|-----------------|
| 445 | TCP | SMB | Credential brute force, lateral movement, ransomware propagation consistent with WannaCry attack pattern |
| 3389 | TCP | RDP | Credential brute force, unauthorised remote desktop access |
| 5985 | TCP | WinRM | Silent remote command execution if credentials are compromised -- living off the land technique |

This exposure reflects deliberate misconfiguration to simulate
the attack surface of a real corporate server configured for
business use rather than a hardened default installation. In
a production environment, access to these services would be
restricted to known management IPs and protected by
multi-factor authentication.

### Microsoft Sentinel: Security Monitoring Platform

Microsoft Sentinel serves as Zaphod Bank's SIEM, providing
centralised log ingestion, threat detection, alerting, and
investigation capability across the environment. It was
selected because it is widely deployed across UK financial
services and public sector organisations, natively integrates
with Windows infrastructure, and is the platform examined
in the SC-200 Microsoft Security Operations Analyst
certification.

Sentinel is deployed on Azure free tier in the UK South
region, ingesting logs forwarded from Windows Server 2022.
Detection rules are written in KQL and mapped to MITRE
ATT&CK techniques, consistent with how professional SOC
teams structure their detection libraries.

---

## Network Architecture

### Networking Mode: Bridged

Both virtual machines are configured using bridged networking,
meaning each VM receives its own IP address on the local
network and operates as an independent device. This was
deliberately chosen over NAT networking, where both VMs
would share the host machine's IP address and be unable to
communicate directly with each other.

Bridged networking is essential for this engagement because
attack simulation requires direct communication between the
threat actor machine and the target. This configuration
reflects the internal network position an attacker would
occupy following initial access to Zaphod's environment,
where internal services are reachable directly without
traversing external firewall controls.

### Network Diagram

```

TBA
```

### Host Resource Allocation

| Component | RAM | CPU Cores | Security Role |
|-----------|-----|-----------|---------------|
| Kali Linux | 2GB | 2 | External threat actor simulating reconnaissance and credential attacks against Zaphod's infrastructure |
| Windows Server 2022 | 3GB | 2 | Target environment representing Zaphod's internal corporate server, hosting exposed services and forwarding telemetry to Sentinel |
| Host OS (Mac) | 11GB remaining | 6 remaining | Sentinel dashboard, investigation, and documentation |
| Microsoft Sentinel | Azure free tier | Cloud managed | Centralised SIEM providing detection, alerting, and incident investigation |

---
