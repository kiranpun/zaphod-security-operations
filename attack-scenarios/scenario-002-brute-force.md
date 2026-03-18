# Scenario 002 | Brute Force Credential Attack

## Engagement Context

This scenario follows directly from Scenario 001, in which network reconnaissance identified three open ports on Zaphod's Windows Server including RDP on port 3389. With an exposed RDP service and a default Administrator account identified, the attacker's next logical step is a credential attack to gain interactive access to the server.

This scenario simulates that attack and validates that Zaphod's Sentinel detection rule catches it before the attacker gains access.


## MITRE ATT&CK Mapping

| Technique | ID | Tactic | Description |
|---|---|---|---|
| Brute Force: Password Guessing | T1110.001 | Credential Access | Systematically attempting passwords from a leaked credential list against a single account |
| Active Scanning | T1595 | Reconnaissance | Prior port scan identified RDP as the attack vector (Scenario 001) |


## Environment

| Component | Details |
|---|---|
| Attacker machine | Kali Linux (192.168.0.118) |
| Target machine | Windows Server 2022 (192.168.0.29) |
| Protocol targeted | RDP (port 3389) |
| Tool used | Hydra v9.6 |
| Password list | rockyou.txt (14,344,399 entries, real leaked credential list) |
| Username targeted | Administrator |


## Scenario Narrative

Having identified open RDP on port 3389 during reconnaissance, the attacker targets the Administrator account with a brute force attack using Hydra. The rockyou.txt password list was chosen because it contains real passwords leaked from previous data breaches, people reuse passwords across services, making leaked lists significantly more effective than randomly generated ones.

The attack works by sending authentication requests to the RDP service repeatedly, trying each password in the list in sequence. Each failed attempt generates a Windows Security Event ID 4625 on the target machine, which is forwarded to Microsoft Sentinel via the Azure Monitor Agent.

Hydra's RDP module is experimental and reduces parallel threads automatically for stability. The attack ran sequentially, trying passwords including common ones such as 123456, iloveyou, princess, and abc123, all real passwords from the rockyou list that millions of people have actually used.


## Why This Attack Works in the Real World

According to CISA advisory AA24-242A, RansomHub affiliates gain initial access primarily through phishing emails, exploitation of known vulnerabilities, and password spraying. RDP is used for lateral movement after initial access is established. This scenario simulates a simplified brute force attack over RDP, which represents the credential access phase of that broader attack chain. The underlying risk is the same, weak credentials on an exposed service give attackers a path into Zaphod's infrastructure.

The attack requires no technical sophistication. Automated tools like Hydra can attempt thousands of passwords per hour with minimal effort from the attacker.

The effectiveness of this attack depends on two conditions both being present: RDP exposed without a VPN requirement, and a weak or default password on the targeted account. Either control alone significantly reduces the risk. Neither control being in place, as simulated in this lab, reflects attack surface conditions documented in real-world financial sector breaches.

If the attacker had successfully authenticated, the likely sequence of events would be:

1. Quiet internal reconnaissance -> mapping other machines and locating valuable data
2. Lateral movement via RDP using Administrator credentials
3. Data exfiltration -> customer records, card data, financial information
4. Ransomware deployment across all reachable machines simultaneously
5. Administrator account lockout -> denying Zaphod the ability to respond
6. Ransom demand combining decryption key with threat to publish stolen data

This double extortion model means even organisations with strong backups face pressure to pay, because the threat of publishing regulated customer data carries FCA, PCI-DSS, and GDPR consequences regardless of whether systems are recovered.


## Attack Execution

**Command run from attacker machine (192.168.0.118):**

```bash
hydra -l Administrator -P /usr/share/wordlists/rockyou.txt 192.168.0.29 rdp -t 4 -W 3
```

**Command breakdown:**
- `-l Administrator` single username to target
- `-P rockyou.txt` password list to iterate through
- `192.168.0.29` target IP (Windows Server)
- `rdp` protocol (Hydra knows RDP runs on port 3389)
- `-t 4` 4 parallel threads (reduced to 1 automatically by Hydra for RDP)
- `-W 3` 3 second wait between attempts

**Note on NLA:** Windows Server 2022 has Network Level Authentication enabled by default, which blocked Hydra's initial RDP connection attempts. NLA was disabled in the lab to simulate an environment with immature security controls, a configuration documented in real-world breach reports as a significant risk factor.

![Hydra Running](detection-rules/kali-linux-hydra-brute-force.png "Hydra brute force running from Kali Linux")
*Figure 1 Hydra v9.6 running from Kali Linux attempting passwords from rockyou.txt against the Administrator account on 192.168.0.29*


## Detection

The brute force attack was detected by the Sentinel analytics rule created in `detection-rules/scenario-001-brute-force.md`. The rule fired within 5 minutes of the attack starting, generating a Medium severity incident with the following details:

- **Source IP:** 192.168.0.118 (Kali Linux, attacker machine)
- **Targeted account:** Administrator
- **Failed attempts:** Multiple within 5 minute window
- **Protocol:** Network logon (Logon Type 3) via NtLmSsp

The `LogonTypeName` field confirmed the attack came over the network rather than interactively, and `LogonProcessName` showed NtLmSsp, confirming NTLM authentication was being used, consistent with a network-based brute force rather than a legitimate interactive session.


## Attacker Perspective

The attack generated detectable noise immediately, every failed password attempt is a logged event. A patient attacker targeting a real organisation would consider slower, lower-volume approaches to stay under detection thresholds. However, most ransomware groups prioritise speed over stealth at the initial access stage, accepting the risk of detection in exchange for getting in quickly across a large number of targets.


## Defender Perspective

The detection fired correctly and provided actionable information: source IP, targeted account, attempt count, protocol, and timestamp. A SOC analyst receiving this alert at Zaphod would follow the response steps in `detection-rules/brute-force-detection.md`, starting with checking whether the Administrator account was locked out and whether any 4624 (successful logon) events followed the failed attempts.

The absence of a successful logon in this scenario means the attack was contained at the credential access stage before any lateral movement or data access occurred.


## References

- MITRE ATT&CK T1110.001 Brute Force: Password Guessing: https://attack.mitre.org/techniques/T1110/001/
- CISA RansomHub Advisory AA24-242A: https://www.cisa.gov/news-events/cybersecurity-advisories/aa24-242a
- Hydra documentation: https://github.com/vanhauser-thc/thc-hydra
- Verizon 2025 Data Breach Investigations Report Financial Services snapshot
- rockyou.txt originally leaked from RockYou.com breach, 2009
