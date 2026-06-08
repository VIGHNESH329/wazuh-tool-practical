# File Integrity Monitoring (FIM) using Wazuh

## Overview

This experiment demonstrates how Wazuh's File Integrity Monitoring (FIM) feature can detect unauthorized modifications to critical system files.

The objective was to understand how the Syscheck module monitors files, compares hashes, and generates alerts when file integrity changes are detected.

---

## Environment

* Operating System: Ubuntu
* Security Platform: Wazuh
* Module Used: Syscheck (File Integrity Monitoring)

---

## Initial Observation

The Syscheck module was enabled, but file changes were not being detected immediately.

Configuration:

```xml
<frequency>43200</frequency>
```

This means Wazuh performs a scan every 12 hours.

---

## Problem

After modifying a monitored file, no immediate alert was generated because real-time monitoring was not enabled.

---

## Solution

Enabled real-time monitoring for the `/etc` directory.

Configuration:

```xml
<directories realtime="yes">/etc</directories>
<directories>/usr/bin,/usr/sbin</directories>
<directories>/bin,/sbin,/boot</directories>
```

After updating the configuration, the Wazuh Manager was restarted.

```bash
sudo systemctl restart wazuh-manager
```

---

## Attack Simulation

Modified the monitored file:

```bash
sudo nano /etc/hosts
```

Added:

```text
127.0.0.1 WAZUH_REALTIME_TEST
```

---

## Detection

Wazuh generated the following alert:

```text
Integrity checksum changed
```

Alert Severity:

```text
Level 7
```

---

## How Detection Works

```text
File Modified
      ↓
Syscheck Recalculates Hash
      ↓
Hash Mismatch Detected
      ↓
Wazuh Generates Alert
```

The alert was generated because the file checksum changed after modification.

---

## Key Concepts Learned

* File Integrity Monitoring (FIM)
* Syscheck Module
* Real-Time Monitoring
* Hash-Based Detection
* Alert Investigation
* Security Monitoring

---

## Conclusion

Successfully configured and tested Wazuh File Integrity Monitoring by enabling real-time monitoring, modifying a protected system file, and validating that Wazuh detected the change through an integrity checksum alert.

This experiment demonstrates how security teams can identify unauthorized modifications to critical files and investigate potential security incidents.
