---
layout: post
title: ProxMox Lab Part 2
date: 2026-02-27
categories:
  - cybersecurity
tags:
  - How-To
image:
  path: /assets/img/ProxMoxLogo.png
description: How-To Guide for setting up a local managed AD Environment in Proxmox
---
Once I built a homelab that I could actually use (in part 1) I was trying to figure out WHAT to do first... Since I spend a lot of time at work in Microsoft Environments (On-Prem, Hybrid, and Cloud) I decided it would be a great to learn more about the architecture of these environments to not only expand my knowledge but to improve my functional capabilities.

Proxmox is a little difficult to get used to, however once there is a baseline knowledge of how to get systems up and running, organizing templates and standards, adding nodes, etc. It is a powerful tool that allows a pretty seamless experience for managing virtualized environments.

The goal of this part of the project is to

1) Create a Virtualized LAN that is dedicated to just this lab
2) Make this Lan's DHCP be controlled by a centralized AD DC server
3) Spin up at least 2 fully functional Windows 11 Computers and 1 Active Directory Domain Controller.
4) Configure a AD DC server with full Domain functionality and join the other windows machines to it.
5) Test the environment:
	- Create Domain accounts and login to the computers with them
	- test some basic GPOs (app restriction, system setting restriction, and application push)






>[!idea] Note on vmbr1 and vmbr0
>- by default, vmbr0 is the internet facing Bridge.
>- In order to have vmbr1 have internet access, we need to use Proxmox host as a router/NAT gateway between vmbr1 (internal) and vmbr0
6) Create an Internal Linux Bridge
	- Node > Network > Create > Linux Bridge
	- Set IP address for vmbr1 - this give it an IP on the Proxmox host
7) Enable IP Forwarding on the Proxmox host
```
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
sysctl -p
```
3) Add NAT rule via iptables
```
iptables -t nat -A POSTROUTING -s 192.168.100.0/24 -o vmbr0 -j MASQUERADE
```
- **NOTE** To make it persistant after reboot, add it to `/etc/network/interfaces` under `vmbr0`.
```
auto vmbr1
iface vmbr1 inet static
        address 192.168.100.1/24
        bridge-ports none
        bridge-stp off
        bridge-fd 0
        post-up iptables -t nat -A POSTROUTING -s 192.168.100.0/24 -o vmbr0 -j MASQUERADE
        post-down iptables -t nat -D POSTROUTING -s 192.168.100.0/24 -o vmbr0 -j MASQUERADE
```
- This is where you should add the *post-up* and *post-down*
- once done, save and do `ifreload -a` to reload network settings without restarting anything.

## Spinning up AD Server, 2nd Windows machine, and adding DHCP functionality within vmbr1.

- NOTES on VM creation
	- 1 Socket 2 cores, 4 GB Ram, `CPU:Host`,  `Bridge: vmbr1`, `Model: VirtIO`,  need to have a secondary ISO image for using the virtio architecture, so don't forget to download the `virtio.win.iso` and set it up as an Additional.
- *Error:*
![[Pasted image 20260223144746.png]]
- A Boot Order, checking configs I see the `scsi0` is set first, swap in my`ide2` which has the server ISO.
![[Pasted image 20260223144930.png]]

- Success! To minimize overhead (and to have some more fun with Powershell) I will be running this purely in Terminal mode. Windows is bloated enough as it is.
![[Pasted image 20260223145157.png]]

- When trying to boot you will notice there is no drives. We will have to Load Driver and install the driver ISO we enabled in `ide0` during boot. This allows us to use the scsi architecture for storage.
- Got it booted to setup! Time to work on the Domain settings!
![[Pasted image 20260223150846.png]]
- **Setting up the Domain**
	- This is all command line
	- **NOTE** - Desktop-less Windows Server drops you int `cmd` and NOT `powershell`, type `Powershell` to start before continuing.
- *Quirk with my ProxMox Setup* - because I am using VirtIO drivers, I have downloaded the one for the virtual harddrive but NOT for the virtual Network Card. I need to find the drive the `iso` is in and run it.
```Powershell
# Find what drive letter your VirtIO ISO is mounted as 
Get-PSDrive -PSProvider FileSystem

# Probably will be one of the drives that isn't mthe main storage point.
Get-ChildItem C:\

# Run it once location is found
D:\virtio-win-gt-x64.msi
```
- BOOM! the Net Adapter shows up now.
![[Pasted image 20260223152038.png]]

- Setup Windows Server's IP and DNS functionality
``` Powershell
# Find your interface index
Get-NetAdapter

# Set static IP
New-NetIPAddress -InterfaceIndex <number> -IPAddress 192.168.100.10 -PrefixLength 24 -DefaultGateway 192.168.100.1

# Set DNS to itself
Set-DnsClientServerAddress -InterfaceIndex <number> -ServerAddresses 192.168.100.10
```

- Install and setup AD DS and create the Domain
```Powershell
# Install AD DS (NOTE: Won't need a restart)
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools

# Then create the domain
Install-ADDSForest -DomainName "lab.local" -DomainNetbiosName "LAB" -InstallDns -Force
```
- After this it might take a little bit for applying Computer Settings
maths4idaho@loner


## Setting up Window Server as the DHCP Server
**Step 1 — Install DHCP Role**

powershell

```powershell
Install-WindowsFeature DHCP -IncludeManagementTools
```

**Step 2 — Authorize DHCP in AD**

powershell

```powershell
Add-DhcpServerInDC -DnsName "LAB-AD-DC1.lab.local" -IPAddress 192.168.100.10
```

**Step 3 — Create the IP Scope**

powershell

```powershell
Add-DhcpServerv4Scope -Name "Lab Network" -StartRange 192.168.100.50 -EndRange 192.168.100.100 -SubnetMask 255.255.255.0
```

**Step 4 — Set Gateway and DNS Options**

powershell

```powershell
Set-DhcpServerv4OptionValue -ScopeId 192.168.100.0 -Router 192.168.100.1 -DnsServer 192.168.100.10
```

**Step 5 — Restart DHCP Service**

powershell

```powershell
Restart-Service DHCPServer
```

---

## Creating new Domain users via commandline
- Simple script for making a bunch of test dummy user accounts plus the admin one.
```powershell
$password = ConvertTo-SecureString "LabPass123!" -AsPlainText -Force

$users = @(
    "eurylochus",
    "elpenor",
    "perimedes",
    "polites",
    "lycaon",
    "amphialos",
    "alkimos",
    "amphidamas",
    "antilochus",
    "tiberius"
)

foreach ($user in $users) {
    New-ADUser -Name $user `
               -SamAccountName $user `
               -UserPrincipalName "$user@domain.com" `
               -AccountPassword $password `
               -Enabled $true
    Write-Host "Created user: $user"
}

# The man himself
New-ADUser -Name "odysseus" `
           -SamAccountName "odysseus" `
           -UserPrincipalName "odysseus@domain.com" `
           -AccountPassword (ConvertTo-SecureString "IHateCirce123" -AsPlainText -Force) `
           -Enabled $true

# Give him his kingdom
Add-ADGroupMember -Identity "Domain Admins" -Members "odysseus"

Write-Host "Odysseus has returned and taken his throne."

```

>[!idea] When working in a AD and wanting copy-paste capabilities there are 2 good options:
>- Set up RDP
>- Set up SSH

#### Setting up SSH
``` Powershell
dism /Online /Add-Capability /CapabilityName:OpenSSH.Server~~~~0.0.1.0
Start-Service sshd 
Set-Service -Name sshd -StartupType Automatic
New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
```
- I don't want to open this to outside of my hypervisor... So I will setup a SSH client using TailScale so that I can use the TailScale IP to ssh into the AD server.
maths4idaho@loner
- **Tailscale Powershell install via curl**
```Powershell
# Install in temp folder and run for the first time
mkdir C:\Temp; Invoke-WebRequest -Uri "https://pkgs.tailscale.com/stable/tailscale-setup-latest.exe" -OutFile "C:\Temp\tailscale-setup-latest.exe" -UseBasicParsing; Start-Process "C:\Temp\tailscale-setup-latest.exe" -Wait

# Start the service and login using the URL on any computer (doesn't have to be the one you are setting tailscale up on). This of course is running from the folder that the tailscale.exe lives (generally in Program Files\Tailscale)
.\tailscale.exe login

```
- Once this is setup you should be able to SSH straight into the AD server from a Tailscale joined computer! Note - SSH will generally drop you into `cmd.exe`, so a good start is to just run `powershell` on entry so all of the commands work.


## GPOs and other baseline automations