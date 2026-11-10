# Windows IT Support & System Administration Labs

## Overview

This repository documents my hands-on training in Windows troubleshooting, networking, Active Directory, and system administration.

I hold a B.S. in Cybersecurity and Information Assurance, along with CompTIA Security+, Network+, and Project+ certifications.

My objective is to develop practical IT support skills through simulated incidents, systematic troubleshooting, infrastructure deployment, and documented resolutions, while progressively building toward Windows system administration.

## Lab Environment

- **Virtualization:** Oracle VirtualBox
- **Operating Systems:** Windows 11 Enterprise, Windows Server 2025
- **Networking:** VirtualBox NAT and host-only networks
- **Infrastructure:** Active Directory Domain Services, Windows DNS
- **Tools:** PowerShell, Windows administrative utilities, and built-in diagnostic tools

## Lab Projects

### LAB-001 — DNS Resolution Failure

**Category:** Windows Networking / IT Support Troubleshooting

Investigated and resolved a simulated DNS resolution failure caused by an intentionally misconfigured DNS server.

**Skills demonstrated:**

- IPv4 addressing, DHCP, and DNS configuration
- Comparing default DNS resolution with direct queries
- Distinguishing network connectivity from name resolution
- PowerShell-based diagnostics and remediation
- Post-remediation verification

[View LAB-001](<LAB-001: DNS Resolution Failure.md>)

### LAB-002 — Windows Server 2025 Active Directory Deployment

**Category:** Windows Server / Systems Administration

**Status:** In Progress

Deploying a Windows Server 2025 Active Directory environment in VirtualBox to develop practical skills in domain administration, network services, and infrastructure troubleshooting.

**Completed work:**

- Windows Server 2025 installation and configuration
- NAT and host-only virtual networking
- Static IPv4 configuration
- Active Directory Domain Services installation
- Creation of the `lab.test` forest
- Domain controller and DNS service verification

**Current troubleshooting:** Resolving unwanted DNS registration on a multihomed domain controller.

**Planned work:**

- Windows 11 domain joining
- Active Directory users, groups, and organizational units
- Group Policy configuration
- Authentication and connectivity troubleshooting
- PowerShell administration

[View LAB-002](LAB-002-Active-Directory/README.md)

## Planned Learning Areas

- Windows services, Event Viewer, and system diagnostics
- Local and domain user administration
- File permissions and access management
- Microsoft Entra ID fundamentals
- User onboarding and offboarding workflows
- PowerShell scripting and automation

## Documentation Approach

Each lab documents its objectives, environment, implementation, diagnostic methodology, verification, and lessons learned.

Configuration commands, technical explanations, and supporting screenshots are included where relevant.

All incidents are simulated training exercises unless otherwise specified.
