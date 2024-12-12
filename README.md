# Building a SOC + Honeynet in Azure (Live Traffic)
![Cloud Honeynet + SOC](https://github.com/user-attachments/assets/641e6b1d-791b-4641-8a1f-02fb8336c6fc)




## Introduction

In this project, I deployed a honeynet in Azure to simulate an insecure environment and monitor malicious activity. Logs from the environment were ingested into a Log Analytics Workspace and analyzed in Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. Security metrics were collected during a 24-hour insecure period. After implementing security hardening measures, I ran the environment for another 24 hours to collect metrics in its secured configuration. Metrics measured include:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Technologies, Azure Components, and Regulations Employed

- Azure Virtual Network (VNet)
- Azure Network Security Groups (NSG)
- Virtual Machines (2 Windows VMs, 1 Linux VM)
- Log Analytics Workspace with Kusto Query Language (KQL) Queries
- Azure Key Vault for Secure Secrets Management
- Azure Storage Account for Data Storage
- Microsoft Sentinel for Security Information and Event Management (SIEM)
- Microsoft Defender for Cloud to Protect Cloud Resources
- Windows Remote Desktop for Remote Access
- Command Line Interface (CLI) for System Management
- PowerShell for Automation and Configuration Management
- <a href="https://csrc.nist.gov/pubs/sp/800/53/r5/upd1/final"> NIST SP 800-53 Revision 5</a> for Security Controls
- <a href="https://csrc.nist.gov/pubs/sp/800/61/r2/final"> NIST SP 800-61 Revision 2</a> for Incident Handling Guidance


## Architecture Before Hardening / Security Controls
![Architecture Before Hardening _ Security Controls](https://github.com/user-attachments/assets/e1d5f5ed-c28c-4ad7-9cdb-e5b3ed12b015)

The "BEFORE" configuration was designed to simulate an unprotected environment, deliberately exposing the virtual resources to the public internet. Virtual Machines had their Network Security Groups (NSGs) configured to allow unrestricted inbound and outbound traffic. Built-in firewalls were disabled to maximize the attack surface and invite malicious actors to exploit vulnerabilities. Azure resources like the Key Vault and Storage Account were deployed with public endpoints, making them accessible from any internet-based client. These measures ensured the environment would attract real-world cyberattacks, allowing for the collection of security event data via Log Analytics Workspace and monitoring in Microsoft Sentinel.

The goal of this phase was to create an easily discoverable environment to entice real-world attacks from malicious actors. The logs captured during this stage were visualized through custom Sentinel workbooks to analyze malicious traffic, track IP origins, and identify attack patterns.


## Architecture After Hardening / Security Controls
![Architecture After Hardening _ Security Controls](https://github.com/user-attachments/assets/a0a04c20-2a52-4a45-9dc6-ea088dfcaeb5)

During this phase, the environment was hardened and security controls were implemented to ensure compliance with NIST SP 800-53 Revision 5 SC-7(3).

- **Network Security Groups (NSGs):** NSGs were updated to deny all traffic by default, permitting only specific connections from the admin workstation's IP address. Only authorized traffic from trusted sources was allowed access to the subnet and virtual machines.

- **Built-in Firewalls:** The built-in firewalls on the Virtual Machines were configured to allow only necessary inbound traffic, effectively reducing the attack surface.

- **Private Endpoints:** Azure Key Vault and Storage Accounts were transitioned from public to Private Endpoints, restricting access to resources within the Virtual Network.

These security measures significantly enhanced the security posture of the environment without hindering the functionality for legitimate operations.

## Attack Maps Before Hardening / Security Controls
**This attack map shows inbound traffic allowed through NSGs before hardening.**
![(Before)-nsg-malicious-allowed-in](https://github.com/user-attachments/assets/f5a438a5-e7f1-48a6-9922-ba4781fadad0)
**This attack map shows brute force attempts targeting a Linux VM.**
![(Before)-syslog-ssh-auth-fail](https://github.com/user-attachments/assets/d5de6435-b486-4e81-86a4-cfc759ac934e)
**This attack map shows brute force attempts targeting a Windows VM.**
![(Before)-windows-rdp-smb-auth-fail](https://github.com/user-attachments/assets/dbbdf0d7-94ee-4ff9-ba7a-1b9d7ed21754)
**This attack map shows attempts to brute force into the SQL server.**
![(Before)-mssql-auth-fail](https://github.com/user-attachments/assets/76a58758-6c03-4903-a1dc-0a2e3bde8809)


## Metrics Before Hardening / Security Controls

The following table shows the metrics measured in the insecure environment over the initial 24 hour period.

Start Time: 12-01-2024 3:52:07 PM

Stop Time: 12-02-2024 3:52:07 PM

| Metric                                                             | Count
| ------------------------------------------------------------------ | -----
| SecurityEvent (Windows VMs)                                        | 58286
| Syslog (Linux VMs)                                                 | 7230
| SecurityAlert (Microsoft Defender for Cloud)                       | 0
| SecurityIncident (Sentinel Incidents)                              | 261
| AzureNetworkAnalytics_CL (Malicious Inbound Traffic Allowed by NSG)| 3605

## Attack Maps After Hardening / Security Controls

```All map queries returned 'no results' due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics measured over a 24-hour period after applying security hardening measures.

Start Time: 12-04-2024 1:15:40 PM

Stop Time:	12-05-2024 1:15:40 PM

| Metric                                                             | Count
| ------------------------------------------------------------------ | -----
| SecurityEvent (Windows VMs)                                        | 687
| Syslog (Linux VMs)                                                 | 0
| SecurityAlert (Microsoft Defender for Cloud)                       | 0
| SecurityIncident (Sentinel Incidents)                              | 0
| AzureNetworkAnalytics_CL (Malicious Inbound Traffic Allowed by NSG)| 0

## Impact of Security Hardening

| Metric                                                             | Percent Change
| ------------------------------------------------------------------ | -----
| SecurityEvent (Windows VMs)                                        | -98.82%
| Syslog (Linux VMs)                                                 | -100.00%
| SecurityAlert (Microsoft Defender for Cloud)                       | 0%
| SecurityIncident (Sentinel Incidents)                              | -100.00%
| AzureNetworkAnalytics_CL (Malicious Inbound Traffic Allowed by NSG)| -100.00%

## Conclusion

This project involved setting up a honeynet in Microsoft Azure to simulate an insecure environment and monitor potential threats. By integrating log sources into a Log Analytics workspace and using Microsoft Sentinel for incident detection and alert triggering, I was able to analyze the security posture of the network before and after applying hardening measures. The comparison between the metrics from the insecure and secured environments demonstrated the effectiveness of security controls, as the number of security events and incidents dropped significantly, a near 100% reduction across the board. This indicates a reduced attack surface and improved security posture.

It is important to note some limiting factors. The first being resource utilization and number of users. If the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls. Second, the secured network was only run for a 24 hour period, limiting scope. The 24-hour monitoring window provided a snapshot of the security improvements but may have been insufficient to detect more advanced or stealthy attack techniques.
