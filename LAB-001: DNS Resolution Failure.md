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
