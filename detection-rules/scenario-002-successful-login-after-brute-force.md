# Detection Rule | Successful Login Following Brute Force

## Threat Description

An attacker who has been conducting a brute force credential attack against the Administrator account successfully authenticates, gaining interactive access to Zaphod's internal Windows Server infrastructure. This represents a confirmed compromise and the highest priority incident a SOC analyst can receive, as the attacker now has full administrative control over the compromised machine and can move laterally, exfiltrate data, or deploy ransomware.



## MITRE ATT&CK

| Field | Detail |
|---|---|
| Tactic | Credential Access, Initial Access |
| Technique | Brute Force: Password Guessing |
| ID | T1110.001 |



## Log Source

Windows Security Event Logs, collected via Azure Monitor Agent and ingested into Microsoft Sentinel (table: `SecurityEvent`, channel: `Security`).

Two Event IDs are correlated in this rule. Event ID 4625 captures failed authentication attempts and Event ID 4624 captures successful authentications. The rule fires when both are present from the same source IP within a 30-minute window, indicating a brute force attack that resulted in successful access.



## Detection Logic

The rule identifies source IPs that generated more than 3 failed login attempts (Event ID 4625) within the last 30 minutes, then checks whether any of those same IPs also produced a successful login (Event ID 4624) within the same window. A match on both conditions from the same IP is treated as a confirmed or likely brute force compromise and fires a High severity alert.

The 30-minute lookback window was chosen to accommodate sustained brute force attacks that may run for several minutes before finding a valid credential, while remaining short enough to avoid false positives from users who fail to authenticate in the morning and log in successfully later in the day.



## KQL Query

```kusto
let FailedLogins = SecurityEvent
| where EventID == 4625
| where TimeGenerated > ago(30m)
| summarize FailedAttempts = count() by IpAddress, Account
| where FailedAttempts > 3;
SecurityEvent
| where EventID == 4624
| where TimeGenerated > ago(30m)
| join kind=inner FailedLogins on IpAddress
| project TimeGenerated, Account, IpAddress, FailedAttempts
```

**Query breakdown:**
- `let FailedLogins` stores IPs with more than 3 failed logins as a variable for reuse
- The main query finds successful logins in the same window
- `join kind=inner` returns only IPs appearing in both result sets, meaning the same IP both failed repeatedly and then succeeded
- `project` selects the relevant columns for the analyst

**Rule scheduling:** Runs every 5 minutes, looks back 30 minutes, alerts when query returns more than 0 results.



## Investigation Steps

Upon receiving this alert, run the following query immediately to establish the full timeline:

```kusto
SecurityEvent
| where EventID in (4624, 4625, 4740)
| where IpAddress == "[source IP from alert]"
| where TimeGenerated > ago(1h)
| project TimeGenerated, EventID, Account, IpAddress, LogonTypeName, LogonProcessName
| order by TimeGenerated asc
```

The analyst should determine:
- The exact time of the successful login relative to the failed attempts
- Whether the account was locked out at any point during the attack (Event ID 4740)
- Whether additional successful logins occurred after the first, indicating persistent access
- Whether the source IP is a known internal asset or an unrecognised host

---

## Response

Steps in order of priority:

1. **Treat as confirmed compromise until proven otherwise** -> a successful login following a brute force pattern must be escalated to Tier 2 immediately. Do not wait for further evidence before escalating.

2. **Terminate active sessions** -> disconnect all active RDP sessions on the compromised server to evict the attacker. On the Windows Server run `query session` to list active sessions and `logoff [session ID]` to terminate them.

3. **Disable RDP immediately** -> prevent the attacker reconnecting by disabling the RDP service: `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 1`

4. **Block the source IP** -> isolate the attacking IP at the firewall level to prevent reconnection attempts via any protocol.

5. **Reset the compromised account credentials** -> force a password reset on the Administrator account. If the attacker has had sufficient dwell time, assume they may have created additional accounts or modified existing ones and audit all privileged accounts.

6. **Check for lateral movement** -> query Sysmon Event ID 3 for outbound connections from the compromised server to other internal hosts during the attacker's session window. Lateral movement is the likely next step.

7. **Report to FCA under DORA** -> a confirmed compromise of a privileged account on production infrastructure at a UK fintech would meet the threshold for a major ICT incident. Zaphod is required to notify the FCA within 4 hours of classification.



## Validation

This rule was tested in the Zaphod Bank lab environment on 18 March 2026. A successful RDP login from Kali Linux (192.168.0.118) was performed following a Hydra brute force attack against the Administrator account. The KQL join correctly correlated the failed attempts (Event ID 4625) with the successful login (Event ID 4624) on the same source IP and returned results confirming the attack pattern.



## Detection Status

| Field | Detail |
|---|---|
| Status | Active |
| Severity | High |
| Created | 18 March 2026 |
| Validated | Yes, live test confirmed |
| Linked scenario | attack-scenarios/scenario-002-brute-force.md |
