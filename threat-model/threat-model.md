# Threat Model

## Zaphod Bank Security Threat Assessment

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
fraudulent transactions, and sale of stolen data.


## Regulatory Context

Zaphod operates under FCA oversight, which governs the protection 
of customer funds, GDPR, which limits how personal data is 
collected and stored, PCI-DSS, which mandates security controls 
around payment card data handling, and DORA, which mandates that 
Zaphod maintains proactive ICT risk management, meets strict 
incident reporting timelines, and formally assesses the cyber 
resilience of third party providers such as IdentityChecked.


## Threat Intelligence

According to the Verizon 2025 Data Breach Investigations Report, 
90% of breaches in the financial sector are financially motivated. 
Unlike other sectors where stolen data must be sold or monetised 
indirectly, Zaphod presents a uniquely direct target. Successful 
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
to pass verification at scale. This is consistent with trusted 
relationship abuse as documented in MITRE ATT&CK technique T1199.

Additionally, the Digital Operational Resilience Act (DORA) 
introduces stricter compliance requirements for financial 
institutions, mandating proactive ICT risk management, incident 
reporting within regulatory timeframes, and formal assessment 
of third party cyber resilience. Whilst transparency 
requirements strengthen accountability, they also risk exposing 
organisational vulnerabilities to sophisticated threat actors 
who monitor public disclosures.


## Threat Actor Groups

The following ransomware groups represent the most active 
and relevant threats to Zaphod Bank's environment, based 
on their known targeting of UK financial services 
organisations and their use of attack techniques simulated 
in this engagement.

### RansomHub

RansomHub operates as a Ransomware as a Service platform, 
meaning the ransomware tooling is developed centrally and 
rented to criminal affiliates who conduct attacks 
independently. This model significantly lowers the technical 
barrier to entry for ransomware attacks, increasing the 
volume of threat actors capable of targeting financially 
motivated victims like Zaphod. RansomHub affiliates 
primarily gain initial access through phishing and RDP 
exploitation, making Zaphod's exposed RDP service on port 
3389 a directly relevant attack surface.

### Akira

Akira specifically targets mid-sized organisations, 
recognising that they hold significant financial data and 
customer information whilst typically operating with less 
mature security defences than large enterprises. This makes 
Zaphod a realistic target profile. Akira commonly gains 
initial access through compromised credentials and RDP, 
then moves laterally using legitimate Windows tooling to 
avoid detection. Their double extortion model, exfiltrating 
data before encrypting it, means a successful attack would 
simultaneously trigger FCA incident reporting obligations, 
GDPR breach notification requirements, and reputational 
damage for Zaphod.

### LockBit

Despite a significant international law enforcement 
disruption operation in February 2024, Operation Cronos, 
in which the NCA, FBI, and Europol seized LockBit 
infrastructure and arrested key members, the group 
continued operating under LockBit 3.0. This resilience 
demonstrates that ransomware groups are distributed and 
difficult to permanently dismantle, and that the threat 
to organisations like Zaphod is persistent regardless of 
law enforcement action. LockBit uses phishing, known 
vulnerability exploitation, and SMB for lateral movement, 
making early detection at the reconnaissance and credential 
attack phase critical before encryption begins.

### Relevance to This Engagement

| Threat Actor | Primary Access Method | Lab Simulation | MITRE Technique |
|---|---|---|---|
| RansomHub | Phishing, RDP exploitation | Hydra brute force on RDP | T1110 |
| Akira | Compromised credentials, RDP | Hydra brute force on SMB | T1110 |
| LockBit | Phishing, SMB lateral movement | Nmap reconnaissance, SMB exposure | T1595 |

---

## Sources

- Verizon Data Breach Investigations Report 2025, Financial 
  Services Snapshot
- The Guardian (2024), Hong Kong deepfake video conference scam: 
  https://www.theguardian.com/world/2024/feb/05/hong-kong-company-deepfake-video-conference-call-scam
- National Crime Agency (2024), Operation Cronos: 
  https://www.nationalcrimeagency.gov.uk/news/nca-leads-international-investigation-targeting-worlds-most-harmful-ransomware-group
