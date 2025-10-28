---
layout: article
title: "RDP Enabled but Port Not Opening on Windows"
description: Complete troubleshooting guide to fix RDP port 3389 not opening despite correct configurations
category: Quick References
date: 2025-10-28
---

> **Tested Environment:** Windows 11 Pro 25H2

## Problem

Attempting to enable remote access via RDP on Windows, but port 3389 was not being opened even with all configurations apparently correct.

## Symptoms

- RDP enabled in registry (`fDenyTSConnections = 0`)
- TermService service running
- Firewall rules configured correctly
- Port 3389 **not in LISTENING state**
- RDP connections failing

## Diagnosis

### 1. Initial Verification

```powershell
# Check if RDP is enabled
Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name 'fDenyTSConnections'
# Expected result: fDenyTSConnections = 0

# Check RDP services
Get-Service -Name TermService,UmRdpService,SessionEnv | Select-Object Name, Status, StartType

# Check port
Test-NetConnection -ComputerName localhost -Port 3389
```

### 2. Problem Identified

The **SessionEnv (Remote Desktop Configuration)** service was **stopped**.

```
Name          Status StartType
----          ------ ---------
SessionEnv   Stopped    Manual    ← PROBLEM HERE
TermService  Running    Manual
UmRdpService Running    Manual
```

## Root Cause

The **SessionEnv** service is a critical dependency for RDP. Without it running, the RDP listener cannot initialize, even if TermService is active.

### SessionEnv Dependencies:
- RPCSS (Remote Procedure Call)
- LanmanWorkstation (Workstation)

## Solution

### Step 1: Start the SessionEnv Service

```powershell
# Method 1: Via PowerShell
Start-Service -Name SessionEnv

# Method 2: Via sc.exe (if method 1 fails)
sc.exe start SessionEnv
```

### Step 2: Configure Automatic Startup

```powershell
# Configure SessionEnv to start automatically
Set-Service -Name SessionEnv -StartupType Automatic

# Configure TermService to start automatically
Set-Service -Name TermService -StartupType Automatic
```

### Step 3: Verify Services Are Running

```powershell
Get-Service -Name TermService,UmRdpService,SessionEnv | Select-Object Name, Status, StartType
```

Expected result:
```
Name          Status StartType
----          ------ ---------
SessionEnv   Running Automatic
TermService  Running Automatic
UmRdpService Running    Manual
```

### Step 4: Test the Port

```powershell
Test-NetConnection -ComputerName localhost -Port 3389
```

If `TcpTestSucceeded : False`, it may be necessary to **restart the computer** for the RDP listener to initialize completely.

### Step 5: Restart Computer (if necessary)

```powershell
Restart-Computer
```

## Firewall Rules Verification

### Check Existing Rules

```powershell
# List firewall rules for RDP
Get-NetFirewallRule | Where-Object {$_.DisplayName -like '*Remote Desktop*'} | Select-Object DisplayName, Enabled, Direction, Action

# Check details of a specific rule
netsh advfirewall firewall show rule name="Allow RDP"
```

### Create Firewall Rule (if necessary)

```powershell
New-NetFirewallRule -DisplayName "RDP Port 3389" -Direction Inbound -LocalPort 3389 -Protocol TCP -Action Allow -Profile Domain,Private,Public
```

## Complete RDP Configuration

### Enable RDP via Registry

```powershell
# Enable RDP
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0

# Allow connections with Network Level Authentication (NLA)
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1
```

### Check Configured Port

```powershell
Get-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name 'PortNumber'
```

## Useful Diagnostic Commands

### Check LISTENING Ports

```powershell
# Check if port 3389 is listening
netstat -ano | findstr ":3389.*LISTENING"

# Check all LISTENING ports
netstat -ano | findstr "LISTENING"
```

### Check RDP-Tcp Configuration

```powershell
Get-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' | Select-Object PortNumber, SecurityLayer, UserAuthentication
```

### Check Service Dependencies

```powershell
# View services that SessionEnv depends on
Get-Service -Name SessionEnv | Select-Object -ExpandProperty ServicesDependedOn

# Detailed service information
sc.exe qc SessionEnv
```

### Check Group Policies

```powershell
# Check if there are policies blocking RDP
Get-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -ErrorAction SilentlyContinue

# Applied group policy results
gpresult /r
```

## Alternative: Change RDP Port

If port 3389 continues with problems, you can use an alternative port:

### Change to Port 3390

```powershell
# 1. Change port in registry
Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name 'PortNumber' -Value 3390

# 2. Create firewall rule for new port
New-NetFirewallRule -DisplayName 'RDP Port 3390' -Direction Inbound -LocalPort 3390 -Protocol TCP -Action Allow -Profile Domain,Private,Public

# 3. Restart computer
Restart-Computer
```

### Connect on New Port

In RDP client, connect using:
```
server-ip:3390
```

## Complete Resolution Script

```powershell
# Script to enable and fix RDP

# 1. Enable RDP
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0

# 2. Configure services for automatic startup
Set-Service -Name SessionEnv -StartupType Automatic
Set-Service -Name TermService -StartupType Automatic

# 3. Start SessionEnv service (critical!)
Start-Service -Name SessionEnv -ErrorAction SilentlyContinue

# 4. Verify services are running
Get-Service -Name TermService,UmRdpService,SessionEnv | Select-Object Name, Status, StartType

# 5. Enable firewall rule (if it doesn't exist)
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"

# OR create new rule
New-NetFirewallRule -DisplayName "RDP Port 3389" -Direction Inbound -LocalPort 3389 -Protocol TCP -Action Allow -Profile Domain,Private,Public -ErrorAction SilentlyContinue

# 6. Test connectivity
Test-NetConnection -ComputerName localhost -Port 3389

# 7. If it doesn't work, restart computer
# Restart-Computer -Force
```

## Important Notes

1. **SessionEnv is critical**: This service MUST be running for RDP to work
2. **Restart may be necessary**: Port changes always require a reboot
3. **Firewall**: Make sure firewall rules are active
4. **NLA**: Network Level Authentication improves security
5. **Alternative port**: Using a port different from 3389 can reduce attack attempts

## References

- SessionEnv Service: Remote Desktop Configuration
- TermService Service: Remote Desktop Services
- Default RDP Port: 3389/TCP
