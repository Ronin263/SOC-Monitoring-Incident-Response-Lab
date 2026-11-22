# Fail2Ban Configuration

## Overview

Fail2Ban was used in the Linux environment to provide automated response to repeated SSH authentication failures.

The SSH jail monitored authentication events and temporarily blocked the source IP address after the configured number of failed attempts.

## Environment

- Operating system: Debian
- Fail2Ban version: 1.0.2-2
- Monitored service: SSH (`sshd`)
- Logging backend: systemd journal
- Firewall: nftables

## Configuration Parameters

The following settings were used during the documented test:

| Parameter | Value | Description |
|---|---:|---|
| `backend` | `systemd` | Reads authentication events from the systemd journal |
| `maxretry` | `5` | Maximum failed attempts before a ban |
| `findtime` | `600` seconds | Time window for counting failures |
| `bantime` | `600` seconds | Duration of the temporary ban |

## Automated Response

During the test, Fail2Ban detected repeated SSH authentication failures and triggered a temporary IP ban using nftables.

The documented test confirmed:

- The SSH jail was active.
- The source IP address was blocked after five failed attempts.
- The ban lasted 600 seconds.
- The source IP was automatically unblocked after the ban expired.
- SSH connectivity was subsequently restored.

## Configuration Provenance

The original Debian configuration files are no longer available.

This documentation was reconstructed from previously recorded test results and configuration details. It describes the settings used during the experiment but is not a copy of the original `jail.local` file.

## Related Documentation

- [Test Results](../../docs/test-results.md)
- [Incident Report](../../docs/incident-report.md)
