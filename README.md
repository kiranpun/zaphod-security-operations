# Zaphod Bank | Security Operations Centre

## Founding SOC Analyst Engagement | Threat Detection Engineering | Microsoft Sentinel

**Analyst:** Kiran

> This project is entirely fictional. Zaphod Bank is a 
> simulated organisation created for educational and 
> portfolio purposes. Any resemblance to real companies 
> is coincidental.

---

## Overview

This repository documents the design, build, and operation of 
Zaphod Bank's security monitoring capability from the ground up. 
As the founding SOC analyst, I was responsible for assessing 
Zaphod's threat landscape, deploying appropriate security tooling, 
simulating real-world attacks relevant to the fintech sector, and 
validating that detections work correctly against those threats.

Every architecture decision, detection rule, and incident report 
reflects independent thinking about what a mid-sized UK fintech 
actually faces and how to detect it, grounded in industry threat 
intelligence and real world reported incidents.

---

## About Zaphod Bank

Zaphod is a mobile-based online banking system that offers users 
banking facilities in the palm of their hands. Its customers are 
individuals who want easy, accessible banking on their mobile 
devices with less friction compared to traditional banks. However, 
this reduced friction introduces identity verification 
vulnerabilities, as digital onboarding processes such as face 
scanning and document uploads can be exploited through synthetic 
identity fraud and deepfake-based KYC bypass.

Zaphod holds large volumes of sensitive data including customer 
personal information, financial data, transaction history, 
investments, loans, bills, payment card information, 
authentication credential hashes, and biometric data such as 
face scans, fingerprints, and behavioural patterns including 
spending habits. Zaphod depends on an external third-party 
provider, IdentityChecked, for KYC verification, introducing 
supply chain risk similar to the 2020 SolarWinds breach, where 
a trusted vendor was compromised to gain access to thousands 
of downstream organisations.

As a holder of both financial data and personally identifiable 
information, Zaphod is a high-value target for financially 
motivated criminal threat actors, for whom successful access 
translates directly into monetary gain through account takeover, 
fraudulent transactions, and sale of stolen data. Zaphod operates 
under FCA oversight, which governs the protection of customer 
funds, GDPR, which limits how personal data is collected and 
stored, PCI-DSS, which mandates security controls around payment 
card data handling, and DORA, which mandates that Zaphod maintains 
proactive ICT risk management, meets strict incident reporting 
timelines, and formally assesses the cyber resilience of third 
party providers such as IdentityChecked.

---

## Threat Intelligence

According to the Verizon 2025 Data Breach Investigations Report, 
90% of breaches in the financial sector are financially motivated. 
Unlike other sectors where stolen data must be sold or monetised 
indirectly, Zaphod presents a uniquely direct target, meaning successful 
access to a customer account or internal server translates 
immediately into real monetary gain through fraudulent fund 
transfers.

System Intrusion is the most prevalent breach pattern in financial 
services, present in 53% of breaches in 2025, up from 36% the 
previous year. According to the DBIR, 75% of system intrusion 
breaches involve ransomware, with attackers gaining initial access 
through vulnerability exploitation, stolen credentials, or 
phishing. Once inside the network, ransomware spreads laterally 
using protocols like SMB running on port 445. This was 
demonstrated at scale by WannaCry in 2017, which infected 
thousands of UK organisations including critical NHS 
infrastructure by exploiting SMB across internal networks. 
This engagement simulates and detects exactly this attack vector.

Social engineering, specifically prompt bombing, represents a 
significant threat to Zaphod's customer base. Attackers obtain 
customer credentials through phishing or data leaks, then 
repeatedly trigger MFA notifications at all hours until the 
customer, frustrated or unaware, approves the request, granting 
the attacker full account access and the ability to transfer 
funds immediately.

---

## Emerging Threats

Deepfake technology represents a growing social engineering 
vector, demonstrated in a 2024 case in Hong Kong where an 
employee at an unnamed multinational firm transferred HK$200m 
(£20m) to fraudsters after being deceived by a fabricated video 
conference call in which all participants, including the CFO, 
were AI generated. This case illustrates how attackers are 
layering emerging technologies onto traditional deception 
techniques to bypass human verification.

AI generated synthetic identities represent a further emerging 
threat specific to Zaphod's onboarding model. Fully fabricated 
personas, complete with AI generated facial imagery, falsified 
documentation, and simulated behavioural patterns, can 
potentially bypass digital KYC verification processes. This 
attack vector exploits the reduced friction of mobile onboarding 
directly, and poses a particular risk where KYC verification is 
delegated to a third party provider such as IdentityChecked, as 
a compromise of that provider could enable fabricated identities 
to pass verification at scale, a trusted relationship abuse 
consistent with MITRE ATT&CK technique T1199.

Additionally, the Digital Operational Resilience Act (DORA) 
introduces stricter compliance requirements for financial 
institutions, mandating proactive ICT risk management, incident 
reporting within regulatory timeframes, and formal assessment 
of third party cyber resilience. Whilst transparency 
requirements strengthen accountability, they also risk exposing 
organisational vulnerabilities to sophisticated threat actors 
who monitor public disclosures.

**Sources:**
- Verizon Data Breach Investigations Report 2025 -- 
  Financial Services Snapshot
- The Guardian (2024) -- Hong Kong deepfake video conference 
  scam: https://www.theguardian.com/world/2024/feb/05/hong-kong-company-deepfake-video-conference-call-scam

---
