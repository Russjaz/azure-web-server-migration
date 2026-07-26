# Azure Web Server Migration

Migration of an on-premises IIS web server to Microsoft Azure, including Azure networking, VM deployment, security configuration, website migration, and verification.

## Project Overview

This project extends my Enterprise On-Prem Infrastructure project by moving the existing IIS website from an on-premises Windows Server environment to Microsoft Azure.

The goal was to practice the basic steps involved in moving a traditional web workload to the cloud while learning how Azure virtual networks, virtual machines, network security groups, public IP addresses, and monitoring work together.

The original website was hosted on the on-premises server `WEB01`. A new Windows Server virtual machine named `web01` was deployed in Azure, IIS was installed, and the existing website files were transferred to the Azure server.

## Architecture

### On-Premises Environment

The original environment used the domain:

`ad.newsolutions.co`

The IIS website was hosted on:

`WEB01`

The website was originally available internally as:

`intranet.ad.newsolutions.co`

### Azure Environment

The Azure environment was created in the `Project3` resource group.

Main resources:

- Virtual network: `newsolutions`
- Address space: `10.50.0.0/16`
- Workload subnet: `10.50.0.0/24`
- Gateway subnet: `10.50.1.0/24`
- Virtual machine: `web01`
- Operating system: Windows Server 2025 Datacenter Azure Edition
- Private IP: `10.50.0.4`
- Network Security Group: `web01-nsg`
- IIS Web Server

The Azure address range was selected so that it did not overlap with the address space used by the on-premises environment.

## Network Design

The Azure virtual network uses:

| Network | Address Range | Purpose |
|---|---|---|
| `newsolutions` VNet | `10.50.0.0/16` | Azure network |
| `default` subnet | `10.50.0.0/24` | Azure workloads and `web01` |
| `GatewaySubnet` | `10.50.1.0/24` | Reserved for a future VPN gateway |

The `GatewaySubnet` was created as preparation for possible future connectivity between the Azure environment and the on-premises network.

A VPN gateway was not deployed as part of this project.

## Azure Virtual Machine

A Windows Server 2025 virtual machine named `web01` was deployed into the `default` subnet.

The VM provides the cloud-based replacement for the original on-premises IIS web server.

IIS was installed on the Azure VM and the existing website content was transferred to the IIS web root.

This allowed the same website that had previously been hosted on-premises to run from the Azure virtual machine.

## Network Security

A Network Security Group named `web01-nsg` controls inbound access to the virtual machine.

The configuration allows the traffic required for the project:

- TCP 80 for HTTP access to the website
- TCP 3389 for administrative RDP access

RDP access was restricted to the administrator's public IP rather than being open to the entire Internet.

The remaining inbound traffic is controlled by Azure's default NSG rules.

## Website Migration

The existing NewSolutions intranet website was copied from the on-premises `WEB01` server to the Azure `web01` virtual machine.

The migrated files were placed in the IIS web root on the Azure server.

After migration, the website successfully loaded from the Azure VM through its public IP address.

This confirmed that:

- The Azure VM was reachable.
- IIS was running.
- The migrated website files were being served correctly.
- The NSG allowed HTTP traffic.

## Verification

The migrated web server was tested from outside the Azure VM.

PowerShell was used to send an HTTP request to the server:

`Invoke-WebRequest http://<public-ip> -UseBasicParsing`

The server returned:

`StatusCode : 200`

`StatusDescription : OK`

This verified that the IIS website was responding successfully over HTTP.

The actual public IP address is not documented here because public IP addresses can change and should not be treated as permanent project documentation.

## Monitoring

Azure Monitor was used to check the status of the virtual machine.

The Azure portal showed:

- VM availability: Available
- Azure outages: None
- Health events: None
- CPU and availability metrics

This provided basic experience with monitoring the health and performance of an Azure virtual machine after deployment.

## Project Screenshots

Proof-of-work screenshots are available in the [`screenshots`](screenshots/) directory.

They document:

1. Azure resource group and deployed resources
2. Virtual network configuration
3. Subnet configuration
4. `web01` virtual machine
5. Network Security Group rules
6. Migrated website running from Azure
7. HTTP `200 OK` verification
8. Azure VM monitoring

## Repository Structure

    azure-web-server-migration/
    ├── README.md
    ├── screenshots/
    │   ├── README.md
    │   ├── 01-resource-group.png
    │   ├── 02-vnet-overview.png
    │   ├── 03-vnet-subnets.png
    │   ├── 04-web01-vm-overview.png
    │   ├── 05-web01-nsg-rules.png
    │   ├── 06-migrated-website.png
    │   ├── 07-http-verification.png
    │   └── 08-web01-monitoring.png
    └── website/

## What I Learned

Through this project I practiced:

- Creating an Azure Virtual Network and subnets
- Planning cloud IP address ranges to avoid overlap with an existing network
- Deploying a Windows Server virtual machine in Azure
- Understanding Azure private and public IP addressing
- Configuring Network Security Group rules
- Installing and using IIS in Azure
- Moving existing website content to a cloud VM
- Testing HTTP connectivity with PowerShell
- Using Azure Monitor to check VM health and basic metrics
- Understanding some of the planning required before connecting an on-premises network to Azure

## Future Improvements

Possible future improvements to the environment include:

- Creating a site-to-site VPN between the on-premises network and Azure
- Configuring private communication between the two environments
- Implementing DNS resolution across the environments
- Adding HTTPS with a valid certificate
- Removing direct public RDP access and using a more secure administrative access method
- Adding Azure monitoring alerts
- Exploring backup and recovery options for the Azure VM

## Related Project

This project builds on my **Enterprise On-Prem Infrastructure** project, where I created the original Windows Server environment containing Active Directory, DNS, DHCP, Certificate Services, file services, and IIS.

The Azure project demonstrates the next step: beginning to move an existing on-premises workload into a cloud environment.
