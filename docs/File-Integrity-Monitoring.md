# Configuring File Integrity Monitoring (Syscheck)

## Overview

File Integrity Monitoring (FIM) is one of Wazuh's core security features. It continuously monitors specified files and directories for changes such as file creation, modification, and deletion. Wazuh uses the **Syscheck** module to detect these changes and generate security alerts in real time.

In this lab, the Windows Wazuh Agent is configured to monitor a test directory and report file system events to the Wazuh Manager.

---

## Objectives

- Configure the Syscheck module on the Windows Agent.
- Monitor a custom directory in real time.
- Restart the Wazuh Agent to apply the configuration.
- Verify that file changes generate alerts in the Wazuh Dashboard.

---

## Prerequisites

Before proceeding, ensure that:

- The Windows Agent is successfully enrolled with the Wazuh Manager.
- The agent status is **Active** in the Wazuh Dashboard.
- The Wazuh Manager and Dashboard services are running.
- A test directory exists on the Windows system.

Example:

```text
C:\Users\<your-username>\Test
```

> **Note:** Replace `<your-username>` with the actual Windows username on the system.

---

# Step 1 – Open the Agent Configuration File

Open the following configuration file using a text editor with Administrator privileges.

```text
C:\Program Files (x86)\ossec-agent\ossec.conf
```

> **Tip:** Right-click Notepad (or your preferred text editor) and select **Run as administrator** before opening the file. Without administrator privileges, changes to this file cannot be saved.

---

# Step 2 – Configure Syscheck

Locate the existing `<syscheck>` section in the configuration file.

> **Important:** Do **not** replace or delete the existing `<syscheck>` block. The default configuration already includes several pre-configured directories and settings (such as `<frequency>`, `<disabled>`, `<scan_on_start>`, and default Windows system directories). Removing these will result in a loss of default FIM coverage.

Add the following `<directories>` entry **inside** the existing `<syscheck>` block, alongside the other `<directories>` entries that are already present.

```xml
<directories realtime="yes">C:\Users\<your-username>\Test</directories>
```

Example of the modified section (showing placement within the existing block):

```xml
<syscheck>

  <!-- Existing default settings (do not remove) -->
  <frequency>43200</frequency>
  <scan_on_start>yes</scan_on_start>

  <!-- Existing default directories (do not remove) -->
  <directories>%WINDIR%\regedit.exe</directories>
  <directories>%WINDIR%\System32\WindowsPowerShell</directories>
  <directories>%WINDIR%\System32\drivers\etc\hosts</directories>
  <!-- ... other default entries ... -->

  <!-- Custom monitored directory (add this line) -->
  <directories realtime="yes">C:\Users\<your-username>\Test</directories>

</syscheck>
```

Save the file after making the changes.

### Understanding Key Syscheck Settings

| Setting | Description |
|---------|-------------|
| `frequency` | Interval (in seconds) between scheduled full scans. Default is **43200** (12 hours). |
| `scan_on_start` | When set to `yes`, a full scan runs when the agent starts. |
| `realtime="yes"` | Enables real-time monitoring using the Windows `ReadDirectoryChangesW` API. Detects changes immediately as they occur. |
| `whodata="yes"` | Enables audit-level monitoring using Windows Security Audit Policies. Reports **who** made the change and **what process** was used. Requires Windows audit policies to be configured. |

> **Note:** Using `realtime="yes"` detects changes immediately but does **not** capture information about which user or process made the change. To capture this level of detail, use `whodata="yes"` instead. The `whodata` option requires Windows audit policies to be enabled on the monitored directory.

---

# Step 3 – Restart the Wazuh Agent

Restart the Windows Agent to apply the updated configuration.

Using PowerShell:

```powershell
Restart-Service WazuhSvc
```

Or use **Windows Services** (`services.msc`) and restart the **Wazuh Agent** service.

---

# Step 4 – Understanding the Baseline Scan

When Syscheck starts monitoring a new directory for the first time, it performs an initial **baseline scan**. During this scan, Syscheck inventories all existing files in the directory and records their attributes (size, hash, permissions, timestamps).

> **Important:** The baseline scan does **not** generate any alerts. Alerts are only generated when changes are detected **after** the baseline has been established. This means that files already present in the directory when monitoring begins will not produce alerts until they are modified, deleted, or new files are created.

This is expected behavior and not an error.

---

# Step 5 – Verify the Configuration

Log in to the Wazuh Dashboard.

Navigate to:

```text
Wazuh → Agents → (Select the Windows Agent) → Integrity Monitoring
```

Alternatively, FIM alerts also appear under:

```text
Wazuh → Security Events
```

Perform one or more of the following actions inside the monitored directory:

- **Create** a new file.
- **Modify** the contents of an existing file.
- **Delete** a file.

Wait a few seconds for the agent to report the event.

Verify that an alert appears in the dashboard indicating the detected file system activity.

### How Syscheck Reports File Operations

| Action Performed | Alerts Generated |
|------------------|-----------------|
| Create a file | One **added** alert. |
| Modify a file | One **modified** alert. |
| Delete a file | One **deleted** alert. |
| Rename a file | Two alerts: one **deleted** alert for the original filename and one **added** alert for the new filename. Syscheck does not have a native rename event. |

> **Note:** Renaming a file is not detected as a single "rename" event. Syscheck treats a rename as a deletion of the old file followed by the creation of a new file, resulting in two separate alerts.

---

## Expected Outcome

After completing these steps:

- The Windows Agent monitors the configured directory in real time.
- File creations, modifications, and deletions are detected and reported.
- Renamed files generate a deletion alert for the old filename and a creation alert for the new filename.
- Wazuh generates File Integrity Monitoring alerts visible in the Dashboard.
- The baseline scan completes silently on first run, with alerts generated only for subsequent changes.

---

# Common Troubleshooting and Expected Errors

| Error | Possible Cause | Solution |
|-------|----------------|----------|
| No alerts generated | Agent was not restarted after editing `ossec.conf`. | Restart the Wazuh Agent service. |
| No alerts after restart | Baseline scan is still in progress. | Wait for the initial scan to complete. Alerts are generated only after the baseline is established. |
| Invalid XML configuration | Incorrect XML syntax in `ossec.conf`. | Validate the XML and ensure the `<directories>` element is inside the existing `<syscheck>` block. |
| Default monitoring stopped | Existing `<syscheck>` content was replaced instead of appended. | Restore the default `ossec.conf` from backup and add only the custom directory entry. |
| Directory not monitored | Incorrect directory path. | Verify that the configured path exists and the username is correct. |
| Agent disconnected | Agent is not communicating with the Manager. | Confirm the agent status is **Active** in the Wazuh Dashboard. |
| No real-time events | `realtime="yes"` attribute is missing. | Add the `realtime="yes"` attribute to the `<directories>` element. |
| Configuration changes not applied | Agent service was not restarted. | Restart the Wazuh Agent service. |
| Permission denied when saving | Text editor not running with administrator privileges. | Reopen the editor as administrator and save again. |

---

# Verification Commands

## Restart the Wazuh Agent

```powershell
Restart-Service WazuhSvc
```

---

## Check the Agent Service

```powershell
Get-Service WazuhSvc
```

---

## View the Agent Log

The Wazuh Agent log can be reviewed to confirm that Syscheck is running and monitoring the configured directory.

```text
C:\Program Files (x86)\ossec-agent\ossec.log
```

Look for entries containing `syscheck` to verify that the module started and the directory was added to the monitoring list.

---

# Key Takeaways

- Syscheck is the Wazuh module responsible for File Integrity Monitoring.
- Real-time monitoring (`realtime="yes"`) detects file system changes immediately using the Windows `ReadDirectoryChangesW` API but does not capture user or process attribution.
- For audit-level detail (who made the change), use `whodata="yes"` with Windows audit policies configured.
- Always **add** custom directory entries to the existing `<syscheck>` block rather than replacing it, to preserve default FIM coverage.
- The first scan of a new directory builds a silent baseline; alerts are generated only on subsequent changes.
- File renames are reported as two separate events (deletion and creation), not as a single rename alert.
- Correct XML syntax in `ossec.conf` is essential for successful monitoring.
- Restarting the Wazuh Agent is required to apply any configuration changes.

---

# References

- [Wazuh Official Documentation – File Integrity Monitoring](https://documentation.wazuh.com/current/user-manual/capabilities/file-integrity/index.html)
- [Wazuh Agent Configuration Guide](https://documentation.wazuh.com/current/user-manual/reference/ossec-conf/index.html)
- [Wazuh Syscheck – Who-data Monitoring](https://documentation.wazuh.com/current/user-manual/capabilities/file-integrity/fim-configuration.html)
- [Microsoft ReadDirectoryChangesW API](https://learn.microsoft.com/en-us/windows/win32/api/winbase/nf-winbase-readdirectorychangesw)