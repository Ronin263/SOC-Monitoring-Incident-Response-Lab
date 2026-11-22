# Wazuh Custom Detection Rules

## Overview

This directory contains custom Wazuh detection rules developed for the SOC Monitoring & Incident Response Lab.

The rules extend the built-in Wazuh capabilities by analyzing Windows Sysmon network connection events.

## Windows Sysmon Rules

Location: `windows/windows_sysmon_rules.xml`

| Rule ID | Detection | Severity |
|---|---|---|
| `100100` | Outbound SSH connection detection | Level 5 |
| `100101` | Outbound HTTPS connection detection | Level 5 |

## Detection Logic

Both rules analyze Sysmon Event ID 3 (Network Connection).

**Rule 100100 – SSH Detection**

Identifies outbound TCP connections initiated by `ssh.exe` to destination port 22.

**Rule 100101 – HTTPS Detection**

Identifies outbound TCP connections initiated to destination port 443.

## Event Processing

1. Sysmon records network connection events.
2. Windows Event Channel makes the events available to the Wazuh Agent.
3. The agent forwards the events to the Wazuh Manager.
4. Custom rules analyze the events and generate security alerts.

## Limitations

The HTTPS rule currently matches outbound TCP connections to port 443 without restricting the executable name.

This is a demonstration rule. Production deployment would require additional filtering and tuning to reduce false positives and alert fatigue.

## Related Documentation

- [Windows Test Results](../../docs/test-results.md)
- [Incident Report](../../docs/incident-report.md)
