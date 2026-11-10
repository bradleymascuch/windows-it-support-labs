# LAB-002 — Windows Server 2025 Active Directory Deployment

**Category:** Windows Server / Active Directory / Systems Administration  
**Status:** In Progress  
**Environment:** Oracle VirtualBox, Windows Server 2025 Standard Evaluation, Windows 11 Enterprise

## 1. Project Overview

This lab documents the deployment of a Windows Server 2025 Active Directory environment in Oracle VirtualBox. Its purpose is to build practical experience with Windows Server administration, TCP/IP networking, Active Directory Domain Services (AD DS), DNS, PowerShell, and systematic troubleshooting.

The project is being developed incrementally. Completed work and verified results are distinguished from planned exercises.

## 2. Lab Architecture

| Component | Configuration |
|---|---|
| Hypervisor | Oracle VirtualBox 7.2 |
| Domain controller | `DC01` |
| Server OS | Windows Server 2025 Standard Evaluation |
| Active Directory domain | `lab.test` |
| NetBIOS domain | `LAB` |
| Client VM | Windows 11 Enterprise (domain join pending) |
| Host-only subnet | `192.168.56.0/24` |
| DC01 host-only interface (`Ethernet 2`) | `192.168.56.10/24` (static; no default gateway) |
| DC01 NAT interface (`Ethernet`) | `10.0.2.15/24` (VirtualBox NAT) |
| Internal DNS server | `192.168.56.10` |

DC01 has two virtual network interfaces. The **host-only adapter** provides connectivity between lab systems, while the **NAT adapter** provides outbound connectivity. The domain controller hosts authoritative DNS for `lab.test`.



> **Design note:** This multihomed configuration is useful for learning, but a single-homed domain controller is generally simpler to administer. We are investigating unwanted DNS registration associated with the NAT adapter.

## 3. Deployment and Verification

### 3.1 Windows Server Installation

Created a Windows Server 2025 Standard Evaluation virtual machine in VirtualBox and completed installation. Configured the VM with NAT and host-only network adapters.

![VirtualBox host-only adapter configuration](screenshots/01-Virtualbox-host-only-adapter.png)

### 3.2 Static Network Configuration

Configured the host-only interface with static IPv4 address `192.168.56.10/24` and no default gateway. Verified that Windows reported the address as manually configured.

The NAT interface provides an outbound path through VirtualBox; the host-only interface is used for internal Active Directory communication.

**Static IPv4 Address Configuration**

![Windows Server static IPv4 configuration](screenshots/02-ip-address-configuration.png)

**IPv4 Address Verification**

![Windows Server IPv4 address verification](screenshots/03-ip-address-verification.png)

**Static Address Origin Verification**

![Windows Server manually configured IPv4 address](screenshots/04-static-ip-origin-verification.png)

### 3.3 Active Directory Domain Services

Installed the AD DS role and management tools:

```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

**AD DS Role Installation**

![Active Directory Domain Services role installation](screenshots/05-adds-role-installation.png)

Created a new forest and installed DNS:

```powershell
Install-ADDSForest -DomainName "lab.test" -DomainNetbiosName "LAB" -InstallDNS
```

Verified the resulting domain:

```powershell
Get-ADDomain
```

Confirmed that the `NTDS`, `DNS`, `Netlogon`, and `KDC` services were running.

**Active Directory Domain Verification**

![Active Directory domain verification](screenshots/06-active-directory-domain-verification.png)

### 3.4 DNS Service Discovery

Queried the domain controller locator SRV record:

```powershell
Resolve-DnsName _ldap._tcp.dc._msdcs.lab.test -Server 192.168.56.10 -Type SRV
```

**DNS SRV Record Verification**

![Active Directory domain controller DNS SRV record verification](screenshots/08-dns-srv-verification.png)

The query identified `dc01.lab.test` as an LDAP service target. Inspection of the DNS response also revealed multiple addresses associated with the domain controller, prompting the troubleshooting investigation below.

## 4. Troubleshooting — Multihomed Domain Controller DNS Registration

### Problem

During Active Directory DNS verification, the domain controller hostname `dc01.lab.test` was found to resolve to multiple IPv4 addresses.

Because DC01 uses separate NAT and host-only network adapters, an address registered from the wrong interface could cause domain clients to attempt connections over an unreachable network.

### Diagnosis

Queried the domain controller's DNS server directly to inspect its registered IPv4 addresses:

```powershell
Resolve-DnsName dc01.lab.test -Type A -Server 192.168.56.10
```

**DNS Records Before Remediation**

![Unwanted DNS registration on a multihomed domain controller](screenshots/07-dns-before-remediation.png)

The query returned two A records:

- `192.168.56.10` — the intended host-only address, reachable by lab clients.
- `10.0.2.15` — the NAT adapter address, not directly reachable from the host-only client network.

This confirmed that DC01 was publishing an address unsuitable for the planned client network.

Examined the DNS registration settings for each network interface:

```powershell
Get-DnsClient | Select-Object InterfaceAlias, RegisterThisConnectionsAddress
```
**DNS Registration Results - Before Remediation**

![DNS registration enabled on both network adapters](screenshots/09-dns-registration-before-remediation.png)

Initially, both Ethernet interfaces permitted DNS address registration. The DNS Server service was also observed listening on multiple addresses. However, listening for DNS queries and registering a server's own addresses in DNS are separate functions.

### Remediation performed

Disabled DNS client address registration on the NAT adapter:

```powershell
Set-DnsClient -InterfaceAlias "Ethernet" -RegisterThisConnectionsAddress $false
```

**NAT Adapter DNS Registration Disabled**

![DNS registration disabled on NAT adapter](screenshots/10-nat-dns-registration-disabled.png)

The previously registered NAT-side A record remained in the authoritative zone. Prepared a targeted removal and previewed it without modifying the zone:

```powershell
Remove-DnsServerResourceRecord -ZoneName "lab.test" -Name "dc01" -RRType "A" -RecordData "10.0.2.15" -WhatIf
```

The `-WhatIf` preview succeeded. **The actual deletion and post-remediation verification have not yet been confirmed in this lab record.**

### Next verification steps

1. Remove only the unwanted `10.0.2.15` A record after confirming the target.
2. Repeat the A-record query and verify that only `192.168.56.10` is returned.
3. Investigate whether Netlogon or another registration mechanism recreates unwanted A/AAAA records.
4. Verify domain controller discovery and client connectivity.

*Evidence to add: before/after DNS query screenshots, with the after screenshot captured only after successful verification.*

## 5. Planned Exercises

- Configure Windows 11 client networking and join `lab.test`.
- Create and manage Active Directory users, groups, and organizational units.
- Configure and test Group Policy.
- Troubleshoot domain authentication, DNS, and connectivity issues.
- Build reusable PowerShell administration scripts.

## 6. Lessons Learned So Far

- DNS name resolution and IP routing solve different problems: a successful lookup does not guarantee connectivity.
- A multihomed server can register addresses that are inappropriate for some client networks.
- Disabling DNS registration on an interface does not automatically remove existing authoritative records.
- `-WhatIf` previews supported PowerShell changes, but does not prove a subsequent operation will succeed.
- Verification should include checking that a configuration issue does not recur.

## 7. Evidence and Documentation

Screenshots will be stored in a `screenshots/` subfolder and embedded inline alongside the relevant implementation or troubleshooting sections. This README will be updated as each stage is completed and verified.

