# INCIDENT INVESTIGATION REPORT #001
**Date:** 21 June 2026
**Analyst:** Tarang Shah
**Severity:** Medium

---

## 1. Executive Summary

On 21st June 2026 at 19:31, an attacker from Kali Linux (10.0.2.4) performed an nmap reconnaissance scan against Windows Server NO-DC (10.0.2.3). 15 alerts were generated — registry values of Windows were modified by nmap scripts but no full system compromise occurred and no data was exfiltrated.

---

## 2. Timeline

| Time | Event |
|------|-------|
| 19:31:06 - 19:31:13 | 15 registry key integrity checksum changes detected (Rule 594, 750) |
| 19:31:16 | Windows logon success — nmap script authenticated to a Windows service (Rule 60106) |
| 19:37:18 | Windows application error event triggered (Rule 60602) |

---

## 3. Indicators of Compromise (IoCs)

| Field | Value |
|-------|-------|
| Attacker IP | 10.0.2.4 (Kali Linux) |
| Victim IP | 10.0.2.3 (Windows Server NO-DC) |
| Attack type | Nmap reconnaissance port scan |
| Total alerts | 37 (15 registry-specific) |
| Rule IDs triggered | 594, 750, 60106, 60602 |
| Protocol | TCP |

---

## 4. MITRE ATT&CK Mapping

| Technique ID | Technique Name | Tactic |
|-------------|---------------|--------|
| T1046 | Network Service Scanning | Reconnaissance |
| T1112 | Modify Registry | Defense Evasion |
| T1565.001 | Stored Data Manipulation | Impact |
| T1078 | Valid Accounts | Initial Access, Persistence |

**Kill Chain Phase:** Phase 1 — Reconnaissance

---

## 5. Analysis

- Attacker ran the command `nmap -Pn -sV -sC -p- 10.0.2.3` which scans all ports without checking whether the host is up or not.
- Nmap scripts interacted with Windows services which caused modification in the registry of Windows.
- This is Phase 1 of the Cyber Kill Chain — Reconnaissance. The attacker was gathering information about open ports and running services.
- No data was exfiltrated and no full system compromise occurred.

---

## 6. Containment Actions

- Block attacker IP (10.0.2.4) at firewall level and investigate all other activity from this IP.
- Review all Wazuh alerts generated during the scan period for missed indicators.
- Inspect Windows Server registry changes flagged by Wazuh — verify no backdoors or persistence mechanisms were installed.
- Check Windows Server for any new services or scheduled tasks created during the scan.

---

## 7. Recommendations

- **Firewall** — Block inbound port scans by only allowing necessary ports (80, 443, 3389) and denying everything else by default.
- **Wazuh** — Create a custom Wazuh rule that specifically detects rapid sequential connection attempts from a single IP — fires alert faster.
- **Sysmon** — Install Sysmon on Windows Server — gives Wazuh much richer Windows telemetry including network connections, process creation, DNS queries — detects attacks earlier and more precisely.

---

*Incident Report #001 | Home SOC Lab | Analyst: Tarang Shah*
*Tools: Wazuh SIEM | Kali Linux (nmap) | VirtualBox*
*Framework: MITRE ATT&CK — https://attack.mitre.org*
