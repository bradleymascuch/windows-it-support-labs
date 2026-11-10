LAB-002 — Windows Server 2025 Active Directory Deployment

Category: Windows Server / Active Directory / Systems Administration
Status: In Progress
Environment: Oracle VirtualBox, Windows Server 2025, Windows 11 Enterprise

1. Project Overview

This lab documents the deployment and configuration of an Active Directory environment using Windows Server 2025 and Oracle VirtualBox.

The objective is to develop practical skills in Windows Server administration, Active Directory Domain Services (AD DS), DNS, network configuration, and troubleshooting.

The environment is designed to support progressively more advanced exercises involving domain-joined workstations, user and group administration, Group Policy, and PowerShell automation.

2. Lab Architecture

The environment consists of a Windows Server 2025 domain controller and a Windows 11 Enterprise client virtual machine.

Infrastructure

Component

Configuration

Hypervisor

Oracle VirtualBox 7.2

Domain Controller

DC01

Server OS

Windows Server 2025 Standard Evaluation

Active Directory Domain

lab.test

NetBIOS Domain

LAB

Client OS

Windows 11 Enterprise

Internal Network

192.168.56.0/24

DC01 Internal IP

192.168.56.10

DC01 NAT IP

10.0.2.15

Internal DNS

192.168.56.10

Network Design

DC01 uses two virtual network adapters:

Adapter 1 — NAT

Provides outbound network connectivity through VirtualBox.

Adapter 2 — Host-only

Provides communication between the domain controller and lab client systems on an isolated virtual network.

The host-only interface is configured with a static IPv4 address of 192.168.56.10/24, without a default gateway.

The domain controller hosts DNS for the lab.test Active Directory domain.

3. Implementation

The following sections will document the completed deployment stages, configuration commands, and verification evidence.

3.1 Windows Server Installation

Documentation pending.

3.2 Network Configuration

Documentation pending.

3.3 Active Directory Domain Services Installation

Documentation pending.

3.4 Domain Controller Verification

Documentation pending.

3.5 DNS Configuration and Troubleshooting

In progress.

4. Planned Administration Exercises

Windows 11 domain joining

Active Directory users, groups, and organizational units

Group Policy configuration and troubleshooting

Domain authentication and connectivity diagnostics

PowerShell administration and automation

5. Lessons Learned

To be completed as the project progresses.
