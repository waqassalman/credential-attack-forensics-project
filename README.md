# Credential Attack & Post Exploitation Forensics Project

## Overview
This Project simulates a full adversary lifecycle against a Windows 10 endpoint, from reconnaissance and credential based initial access through post exploitation shell activity and forensic reconstruction of the incident. It was built to practice both the offensive and defensive sides of a compromise: gaining access the way an attacker would, then investigating that same access the way a forensic analyst would.

**Attacker machine:** Kali Linux (192.168.1.103)
**Target machine:** Win10 Client 01 (192.168.1.102)

## Environment Setup
The target endpoint was intentionally weakened to reflect common enterprise misconfigurations:

* Microsoft Defender real time protection, cloud delivered protection, and tamper protection disabled
* Windows Firewall lowered across Domain, Private, and Public profiles
* Legacy SMB 1.0/CIFS file sharing enabled
* A local administrator account created with a weak, guessable password

## Attack Chain
1. **Reconnaissance:** Nmap scans identified open RPC, NetBIOS, and SMB ports (135, 139, 445) on the target
2. **Exploitation:** Used Impacket's `psexec.py` to upload a payload and register a temporary Windows service, then used `wmiexec.py` to gain an interactive shell over WMI/SMB without dropping a binary to disk
3. **Post exploitation:** Verified access with `whoami`, `net user`, and `tasklist` on the compromised host

## Forensic Investigation
1. **Image acquisition:** Captured a logical disk image with FTK Imager and verified MD5/SHA1 hash integrity between the source and the image
2. **Artifact recovery:** Used Autopsy to recover browser download history showing payload retrieval from the attacker's IP, along with search history confirming tool staging
3. **Event log analysis:** Correlated Event ID 4624 (successful network logon, Logon Type 3) and Event ID 7045 (service installation) in the Security and System logs to confirm the attacker's access and persistence mechanism
4. **Network analysis:** Filtered Wireshark captures on SMB2 traffic and on the attacker's IP to confirm the session negotiation and command execution over the network

## Tools Used
Nmap, Impacket (psexec, wmiexec), FTK Imager, Autopsy, Windows Event Viewer, Wireshark

## Key Takeaways
This lab reinforced how a handful of common misconfigurations (a weak local admin account, disabled endpoint protection, and legacy SMB) can be chained into full remote code execution, and how that activity leaves a consistent, correlatable trail across browser artifacts, event logs, and network captures. Full write up with screenshots is included in this repo.

## Report
See `credential-attack-forensics-report.pdf` for the full write up with screenshots of each step.
