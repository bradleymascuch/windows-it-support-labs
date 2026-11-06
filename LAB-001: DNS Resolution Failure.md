# LAB-001: DNS Resolution Failure

## Incident Summary

**Environment:** Windows 11 virtual machine running in Oracle VirtualBox with NAT networking.

**Incident type:** Simulated workstation DNS misconfiguration.

**Symptoms:** The workstation could establish external network connectivity but could not resolve domain names through its configured DNS server.

**Outcome:** Identified the nonresponsive DNS server configuration, restored automatic DNS settings, and confirmed successful name resolution.

## Initial Configuration

| Setting | Value |
|---|---|
| IPv4 address | 10.0.2.15 |
| Subnet mask | 255.255.255.0 |
| Default gateway | 10.0.2.2 |
| DHCP server | 10.0.2.2 |
| Original IPv4 DNS server | 192.168.1.1 |


## Fault Simulation

To reproduce a workstation DNS failure, I manually configured the Windows 11 virtual machine to use a nonresponsive DNS server.

```powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses "192.0.2.1"
```

The address `192.0.2.1` belongs to the TEST-NET-1 documentation range. It was used to simulate an incorrect DNS server configuration.

**Expected behavior:** The workstation would retain network connectivity but fail to resolve domain names through its default DNS server.

## Diagnostic Process

### Test 1: Inspect Network Configuration

**Command:**

```powershell
ipconfig /all
```

**Observations:**

- IPv4 address: `10.0.2.15`
- DHCP server: `10.0.2.2`
- Configured DNS server: `192.0.2.1`
- DHCP remained enabled.

**Analysis:**

The workstation continued to receive its IPv4 configuration through DHCP, but its DNS server had been manually overridden.

This demonstrated that DHCP-enabled networking does not necessarily mean DNS server addresses are being obtained automatically.

### Test 2: Check Default DNS Resolution

**Command:**

```powershell
nslookup google.com
```

**Result:** DNS query timed out.

**Analysis:**

The workstation could not obtain a response from its configured DNS server.

This suggested a DNS resolution problem but did not, by itself, establish whether the cause was local configuration, DNS server availability, or network connectivity.

### Test 3: Query an Alternative DNS Server

**Command:**

```powershell
nslookup google.com 8.8.8.8
```

**Result:** DNS resolution succeeded.

**Analysis:**

The workstation could successfully resolve domain names when explicitly directed to a functioning DNS server.

This indicated that DNS queries could reach an external resolver and narrowed the investigation to the default DNS configuration or its configured server.

### Test 4: Review External Connectivity

External TCP connectivity had been verified during baseline testing.

**Command:**

```powershell
Test-NetConnection 1.1.1.1 -Port 443
```

**Baseline result:**

```text
TcpTestSucceeded : True
```

**Analysis:**

Successful TCP connectivity to an external IP address demonstrated that the workstation had a functioning external network path during baseline testing.

Combined with the successful alternative DNS query during the incident, this supported the conclusion that the problem was DNS-specific rather than a complete loss of connectivity.

## Diagnostic Summary

| Test | Observed Result | Interpretation |
|---|---|---|
| `ipconfig /all` | DNS set to 192.0.2.1 | Incorrect DNS configuration identified |
| Default `nslookup` | Timed out | Configured resolver not responding |
| `nslookup` using 8.8.8.8 | Successful | Alternative DNS resolution functional |
| `Test-NetConnection 1.1.1.1 -Port 443` | Successful at baseline | External TCP connectivity established before fault |

## Root Cause

The Ethernet adapter's DNS server configuration had been manually overridden to use `192.0.2.1`.

Because this address did not provide a functioning DNS service, domain-name queries sent to the configured resolver timed out.

**Root cause classification:** Local workstation network misconfiguration.

## Remediation

Restored automatic DNS server configuration using an elevated PowerShell session.

```powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ResetServerAddresses
```

This removed the manually configured DNS server addresses and restored the adapter's default DNS configuration.

## Post-Remediation Verification

The following commands were used to check the restored configuration and network functionality:

```powershell
Get-DnsClientServerAddress -InterfaceAlias "Ethernet"
nslookup google.com
Test-NetConnection 1.1.1.1 -Port 443
```

**Outcome:** Default DNS resolution was successfully restored.

The incident was resolved without changing the workstation's IP addressing or default gateway.

## Lessons Learned

1. **Separate connectivity from name resolution.** A workstation may have functional IP connectivity while DNS resolution fails.

2. **Verify the configured DNS server.** DHCP can remain enabled even when DNS settings have been manually overridden.

3. **Compare DNS resolvers.** Querying a known alternative resolver helps distinguish a default DNS configuration problem from broader DNS connectivity failures.

4. **Use a structured troubleshooting process.** Inspect configuration, establish a hypothesis, test it, apply remediation, and verify the outcome.

5. **Document observed evidence.** Diagnostic conclusions should be supported by actual command output rather than assumptions.

## Lab Scope

This was a controlled troubleshooting exercise performed in a Windows 11 VirtualBox environment.

The DNS misconfiguration was deliberately introduced for training purposes. The exercise demonstrates practical diagnostic methods and recovery procedures.
