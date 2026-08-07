# Accessing the Wazuh Dashboard

## Overview

The Wazuh Dashboard is the web-based interface of the Wazuh platform. It provides administrators and security analysts with a centralized location to monitor security events, manage enrolled agents, analyze alerts, investigate incidents, and configure various Wazuh components. After successfully installing the Wazuh platform, the dashboard can be accessed securely through a web browser using the Ubuntu server's IP address.

---

## Objectives

- Identify the Ubuntu server's IP address.
- Access the Wazuh Dashboard through a web browser.
- Accept the self-signed SSL certificate.
- Authenticate using the administrator credentials.
- Verify that the Wazuh Dashboard is functioning correctly.

---

## Prerequisites

Before accessing the dashboard, ensure that:

- The Wazuh Manager service is running.
- The Wazuh Indexer service is running.
- The Wazuh Dashboard service is running.
- The Ubuntu virtual machine is connected to the network.
- HTTPS (Port **443**) is accessible.

---

## Step 1 – Identify the Ubuntu Server IP Address

Run the following command to identify the IP address assigned to the Ubuntu server.

```bash
ip addr show
```

Alternatively, if the **net-tools** package is installed, you can use:

```bash
ifconfig
```

Locate the IPv4 address associated with your active network interface.

Example:

```text
192.168.1.120
```

---

## Step 2 – Open the Wazuh Dashboard

Open a modern web browser and navigate to the following address:

```text
https://<ubuntu-server-ip>
```

Example:

```text
https://192.168.1.120
```

---

## Step 3 – Accept the Security Certificate

Since Wazuh uses a self-signed SSL certificate by default, your browser will display a security warning.

Select:

**Advanced** → **Proceed to the website**

> **Note:** This warning is expected in a home lab environment. In production deployments, replace the self-signed certificate with one issued by a trusted Certificate Authority (CA).

---

## Step 4 – Log In to the Dashboard

Log in using the administrator credentials generated during the Wazuh installation.

> **Important:** Save these credentials when they are displayed during installation, as they are required to access the dashboard.

## Expected Outcome

After completing these steps:

- The Wazuh Dashboard is accessible through a web browser.
- The administrator successfully authenticates.
- The Overview dashboard loads without errors.
- Communication between the Dashboard, Indexer, and Manager is operational.
- The environment is ready for agent enrollment and security monitoring.

---

# Common Troubleshooting and Expected Errors

| Error | Possible Cause | Solution |
|-------|----------------|----------|
| `This site can't be reached` | Incorrect IP address or the server is offline. | Verify the server IP address using `ip addr show` and ensure the VM is running. |
| `ERR_CONNECTION_REFUSED` | Wazuh Dashboard service is not running. | Start the service using `sudo systemctl start wazuh-dashboard`. |
| `ERR_CONNECTION_TIMED_OUT` | Network configuration issue or firewall blocking traffic. | Verify VM networking and ensure HTTPS (Port 443) is accessible. |
| `NET::ERR_CERT_AUTHORITY_INVALID` | Self-signed SSL certificate. | Accept the browser security warning in a lab environment. |
| `Login failed` | Incorrect administrator credentials. | Use the credentials generated during installation or reset the administrator password if necessary. |
| `Blank Dashboard` | Dashboard cannot communicate with the Wazuh Indexer. | Verify that the Wazuh Indexer service is running. |
| `502 Bad Gateway` | Dashboard failed to communicate with the Indexer. | Restart both the Dashboard and Indexer services and review the logs. |
| `Internal Server Error (500)` | Dashboard configuration issue. | Review the dashboard logs and verify the configuration files. |

---

# Verification Commands

## Check Wazuh Dashboard Status

```bash
sudo systemctl status wazuh-dashboard
```

---

## Check Wazuh Indexer Status

```bash
sudo systemctl status wazuh-indexer
```

---

## Check Wazuh Manager Status

```bash
sudo systemctl status wazuh-manager
```

---

## Verify HTTPS Port

```bash
sudo ss -tulnp | grep 443
```

---

## Verify Local Dashboard Response

```bash
curl -k https://localhost
```


# Key Takeaways

- The Wazuh Dashboard provides a centralized interface for monitoring, investigating, and managing security events.
- The default installation uses a self-signed SSL certificate, which generates an expected browser security warning in a home lab environment.
- Successful access to the dashboard confirms that the Wazuh Manager, Indexer, and Dashboard services are communicating correctly.
- Verifying service status before troubleshooting significantly reduces deployment issues.
- The dashboard serves as the primary interface for enrolling agents, monitoring alerts, and managing the Wazuh environment.

---

# References

- [Wazuh Official Documentation](https://documentation.wazuh.com/)
- [Ubuntu Server Documentation](https://ubuntu.com/server/docs)
- [Linux `ip` Command Manual](https://man7.org/linux/man-pages/man8/ip.8.html)