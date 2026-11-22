# Suricata Configuration

## Overview

Suricata was deployed as a Network Intrusion Detection System (NIDS) to monitor network traffic and identify suspicious SSH activity.

It provided network-level visibility during the SSH brute-force detection experiment.

## Environment

- Tool: Suricata
- Version: 7.0.10
- Deployment: Linux environment
- Detection mode: Intrusion Detection System (IDS)
- Monitored protocol: SSH
- Monitored port: TCP/22

## Custom Detection Rule

A custom Suricata rule was used to identify SSH traffic.

- Rule SID: `1000001`
- Protocol: TCP
- Destination port: 22
- Purpose: Detect SSH network activity during the experiment.

## Role in the SOC Lab

Suricata provided network-level detection, complementing authentication monitoring performed by Wazuh.

The generated alerts were reviewed alongside Wazuh and system logs to analyze the attack timeline and identify relevant source IP addresses.

This correlation was performed during manual analysis rather than through an automated integration.

## Configuration Provenance

The original Suricata configuration files are no longer available.

This documentation summarizes the settings and detection capabilities confirmed by the recorded experiment. It does not reproduce the original configuration or the exact custom rule syntax.

## Related Documentation

- [Test Results](../../docs/test-results.md)
- [Incident Report](../../docs/incident-report.md)
