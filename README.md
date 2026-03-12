# Zaphod Bank | Security Operations Centre

## Founding SOC Analyst Engagement | Threat Detection Engineering | Microsoft Sentinel

**Analyst:** Kiran

> This project is entirely fictional. Zaphod Bank is a 
> simulated organisation created for educational and 
> portfolio purposes. Any resemblance to real companies 
> is coincidental.

> This project is actively in progress. 
> New scenarios, detection rules, and incident 
> reports are added as the lab develops.

---

## Overview

This repository documents the design, build, and operation of 
Zaphod Bank's security monitoring capability from the ground up. 
As the founding SOC analyst, I am responsible for assessing 
Zaphod's threat landscape, deploying appropriate security tooling, 
simulating real-world attacks relevant to the fintech sector, and 
validating that detections work correctly against those threats.

Every architecture decision, detection rule, and incident report 
reflects independent thinking about what a mid-sized UK fintech 
actually faces and how to detect it, grounded in industry threat 
intelligence and real world reported incidents.

## About Zaphod Bank
Zaphod Bank is a fictional mid-sized UK mobile banking platform 
serving as the basis for this engagement. The threat model covers 
Zaphod's regulatory obligations under FCA, GDPR, PCI-DSS and DORA, 
attack vectors identified from the Verizon 2025 DBIR financial 
services snapshot, and emerging threats including deepfake social 
engineering and AI generated synthetic identities.

Full analysis: [threat-model/threat-model.md](threat-model/threat-model.md)

## My Role

As Zaphod Bank's founding SOC analyst, I am responsible for 
researching the threat landscape facing UK financial institutions, 
identifying the attack vectors most relevant to Zaphod's 
environment, and proactively designing and implementing detection 
capabilities to identify and respond to those threats before they 
could cause material harm to the business.

## Environment

The engagement environment simulates Zaphod Bank's internal 
corporate infrastructure, consisting of a Windows Server 2022 
target machine, a Kali Linux attack machine, and Microsoft 
Sentinel as the centralised SIEM. Both virtual machines run 
locally on Apple Silicon via UTM, connected via bridged 
networking to reflect realistic internal network conditions.

Full architecture documentation, network diagram, and component 
decisions are available in [architecture/environment.md](architecture/environment.md).

**Sources:**
- Verizon Data Breach Investigations Report 2025 -- 
  Financial Services Snapshot
- The Guardian (2024) -- Hong Kong deepfake video conference 
  scam: https://www.theguardian.com/world/2024/feb/05/hong-kong-company-deepfake-video-conference-call-scam

---
