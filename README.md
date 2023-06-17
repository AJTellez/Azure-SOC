# Building a SOC + Honeynet in Azure (Live Traffic)
![Cloud Honeynet / SOC](https://i.imgur.com/5CUiOJm.png)

## Introduction

In this project, I build a mini honeynet in Azure and ingest log sources from various resources into a Log Analytics workspace, which is then used by Microsoft Sentinel to build attack maps, trigger alerts, and create incidents. I measured some security metrics in the insecure environment for 24 hours, apply some security controls to harden the environment, measure metrics for another 24 hours, then show the results below. The metrics we will show are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel
  
## Architecture Before Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/bhfsT7b.png)

For the "BEFORE" metrics, all resources were originally deployed, exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls wide open, and all other resources are deployed with public endpoints visible to the Internet.

Potential issues that can arise:
- By having Network Security Groups (NSGs) wide open, it means that there are no specific rules defined to restrict access to the virtual machines (VMs). This allows any incoming traffic from the internet to reach the VMs, which increases the risk of unauthorized access, potential attacks, and data breaches.
- If the built-in firewalls on the VMs are wide open, it means that all incoming and outgoing network traffic is allowed. This configuration exposes the VMs to various security risks, including unauthorized access, malware infections, and potential data exfiltration.
- Deploying resources with public endpoints means they are directly accessible from the internet without any form of restriction or protection. This increases the surface area for potential attacks and makes it easier for attackers to discover and exploit vulnerabilities in your resources.


## Architecture After Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/PZU95F3.png)

For the "AFTER" metrics, Network Security Groups were hardened by blocking ALL traffic with the exception of my admin workstation, and all other resources were protected by their built-in firewalls as well as Private Endpoint

Implementing security measures:

- NSGs are used to control inbound and outbound network traffic in Azure. In this case, the NSGs were hardened, which means that the rules were configured to block ALL traffic except for the traffic originating from the admin workstation. By doing this, only the admin workstation is allowed to communicate with the protected resources, ensuring that unauthorized access from other sources is denied.
- The other resources, apart from the admin workstation, were protected by their own built-in firewalls. These firewalls are specific to the individual resources and provide an additional layer of protection. They help monitor and control incoming and outgoing network traffic for the respective resources, mitigating potential security risks.
- Private Endpoint is a network interface that connects securely to an Azure resource over a private IP address within a virtual network. By utilizing Private Endpoint, the resources are shielded from public exposure and can only be accessed through the private network. This further enhances security by limiting access to the resources and reducing the attack surface.

## Attack Maps Before Hardening / Security Controls
![NSG Allowed Inbound Malicious Flows](https://i.imgur.com/17KNccI.png)<br>
![Linux Syslog Auth Failures](https://i.imgur.com/7bxfyld.png)<br>
![Windows RDP/SMB Auth Failures](https://i.imgur.com/w6AReoV.png)<br>

## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2023-06-06 03:25:03
Stop Time 2023-06-07 03:25:03

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 15733
| Syslog                   | 775
| SecurityAlert            | 0
| SecurityIncident         | 239
| AzureNetworkAnalytics_CL | 369

## Attack Maps Before Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2023-06-07 03:41:56
Stop Time	2023-06-08 03:41:56

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 1033
| Syslog                   | 30
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

## Conclusion

In this project, a mini honeynet was constructed in Microsoft Azure and log sources were integrated into a Log Analytics workspace. Microsoft Sentinel was employed to trigger alerts and create incidents based on the ingested logs. Additionally, metrics were measured in the insecure environment before security controls were applied, and then again after implementing security measures. 

Overall, by implementing these security measures, the network infrastructure is fortified, allowing only authorized traffic from the admin workstation and providing additional layers of protection through built-in firewalls and Private Endpoint. This approach significantly minimizes the risk of unauthorized access, potential threats, and malicious activities targeting the protected resources.

It is worth noting that if the resources within the network were heavily utilized by regular users, it is likely that more security events and alerts may have been generated within the 24-hour period following the implementation of the security controls.
