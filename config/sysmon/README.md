# Sysmon Configuration

## Overview

The Windows endpoint monitoring module uses Sysmon to collect detailed system and network telemetry from a Windows 11 virtual machine.

## Configuration Details

- **Configuration source:** [SwiftOnSecurity/sysmon-config](https://github.com/SwiftOnSecurity/sysmon-config)
- **Configuration version:** 74
- **Configuration date:** July 8, 2021
- **License:** Creative Commons Attribution 4.0 International (CC BY 4.0)
- **Sysmon version:** 15.22
- **Endpoint:** Windows 11 VM

## Implementation

The original SwiftOnSecurity configuration was deployed without modifications.

Sysmon records endpoint telemetry in the `Microsoft-Windows-Sysmon/Operational` event channel.

The Wazuh Agent collects these events through the Windows EventChannel integration and forwards them to the Wazuh Manager for analysis.

## Monitored Events

This project uses Sysmon Event ID 3 (Network Connection) to monitor selected outbound SSH and HTTPS connections.

Custom Wazuh rules 100100 and 100101 analyze these events and generate corresponding security alerts.

## Attribution

The Sysmon configuration was created by SwiftOnSecurity.

Source: https://github.com/SwiftOnSecurity/sysmon-config

License: Creative Commons Attribution 4.0 International (CC BY 4.0).

The original configuration is used without modifications.
