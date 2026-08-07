# Installing the Wazuh Agent on Windows

## Overview

The Wazuh Agent is installed on monitored endpoints to collect security-related events and system information. It forwards this data securely to the Wazuh Manager, where it is analyzed to detect threats, monitor system integrity, and generate security alerts. In this lab, the Wazuh Agent is installed on a Windows host to prepare it for enrollment and centralized monitoring.

---

## Objectives

- Download the latest Wazuh Agent for Windows.
- Install the Wazuh Agent using the MSI installer.
- Verify that the Wazuh Agent service is installed and running.
- Prepare the Windows host for agent enrollment.

---

## Prerequisites

Before installing the Wazuh Agent, ensure that:

- The Wazuh Manager is installed and running.
- The Windows host has network connectivity to the Wazuh Manager.
- Administrator privileges are available on the Windows system.
- Windows Defender Firewall allows communication with the Wazuh Manager (if applicable).

---

## Step 1 – Download the Wazuh Agent

Download the latest Wazuh Agent MSI package from the official Wazuh documentation.

Download:

**Wazuh Agent for Windows (MSI Installer)**

Save the installer to the Windows host.

---

## Step 2 – Launch the Installer

Locate the downloaded MSI package and double-click it to start the installation wizard.

If prompted by **User Account Control (UAC)**, select **Yes** to allow the installer to make changes.

---

## Step 3 – Install the Wazuh Agent

Proceed through the installation wizard using the default settings.

Click:

**Next → Install → Finish**

> **Note:** The MSI installer installs the Wazuh Agent on the Windows system. Configuration of the Wazuh Manager address and agent enrollment can be completed in a later step or during unattended installation using deployment parameters.

---

## Step 4 – Verify the Installation

After the installation completes, verify that the Wazuh Agent service has been installed successfully.

### Using Windows Services

1. Press **Win + R**.
2. Type:

```text
services.msc
```

3. Locate **Wazuh Agent**.

Verify that:

- Status is **Running**.
- Startup Type is **Automatic**.

---

### Using PowerShell

```powershell
Get-Service WazuhSvc
```

Expected output:

```text
Status   Name       DisplayName
------   ----       ----------------
Running  WazuhSvc   Wazuh Agent
```

---

## Expected Outcome

After completing these steps:

- The Wazuh Agent is successfully installed on the Windows host.
- The Wazuh Agent service is running.
- The Windows endpoint is ready for configuration and enrollment with the Wazuh Manager.

---

# Common Troubleshooting and Expected Errors

| Error | Possible Cause | Solution |
|-------|----------------|----------|
| Installation failed | Installer not executed with administrator privileges. | Run the MSI installer as an administrator. |
| Windows blocked the installer | Windows Defender SmartScreen warning. | Select **More info** → **Run anyway** if the installer is downloaded from the official Wazuh website. |
| MSI installation terminated prematurely | Corrupted installer or missing Windows Installer service. | Download the installer again and verify that the Windows Installer service is running. |
| Wazuh Agent service not found | Installation did not complete successfully. | Reinstall the Wazuh Agent. |
| Wazuh Agent service stopped | Configuration issue or installation failure. | Restart the service and review the Wazuh Agent log. |
| Agent not visible in the Dashboard | The agent has not yet been configured or enrolled with the Wazuh Manager. | Configure the Manager address and complete the agent enrollment process. |
| Access denied | Installation attempted without administrator privileges. | Run the installer using an administrator account. |

---

# Verification Commands

## Check the Wazuh Agent Service

```powershell
Get-Service WazuhSvc
```

---

## Start the Service

```powershell
Start-Service WazuhSvc
```

---

## Restart the Service

```powershell
Restart-Service WazuhSvc
```

---

## Stop the Service

```powershell
Stop-Service WazuhSvc
```

---

## View the Agent Log

```text
C:\Program Files (x86)\ossec-agent\ossec.log
```

---

# Screenshots

Include the following screenshots in this section:

1. Wazuh Agent Download Page
2. MSI Installer Wizard
3. Installation Progress
4. Installation Completed
5. Wazuh Agent Running in Windows Services
6. PowerShell Service Verification

---

# Key Takeaways

- The Wazuh Agent collects endpoint telemetry and securely forwards it to the Wazuh Manager.
- Administrator privileges are required to install the agent.
- The default MSI installer performs the software installation; agent configuration and enrollment can be completed afterward.
- Verifying the Windows service confirms that the installation completed successfully.
- After installation, the Windows endpoint is ready to be configured and enrolled with the Wazuh Manager for centralized security monitoring.

---

# References

- Wazuh Official Documentation
- Microsoft Windows Installer Documentation
- Microsoft PowerShell Documentation