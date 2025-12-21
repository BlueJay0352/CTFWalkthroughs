CyberDefenders CTF Lab Lockdown
---


https://cyberdefenders.org/blueteam-ctf-challenges/lockdown

#### Scenario

TechNova Systems’ SOC has detected suspicious outbound traffic from a public-facing IIS server in its cloud platform—activity suggestive of a web-shell drop and covert connections to an unknown host.
As the forensic examiner, you have three critical artefacts in hand: a PCAP capturing the initial traffic, a full memory image of the server, and a malware sample recovered from disk. Reconstruct the intrusion and all of the attacker’s activities so TechNova can contain the breach and strengthen its defenses.



#### Machine Environment 

VMware Workstation Local Lab

remnux

Windows 10 w/ FLARE (https://github.com/mandiant/flare-vm)

### 1. After flooding the IIS host with rapid-fire probes, the attacker reveals their origin. Which IP address generated this reconnaissance traffic?
---

Wireshark when filtered for “http" (IIS is a service which runs http) you can see the rapid probes sent by the attacker.

<img width="493" height="738" alt="image" src="https://github.com/user-attachments/assets/0a0855a6-2b63-484e-94b4-183db85652c2" />


### 2. Zeroing in on a single open service to gain a foothold, the attacker carries out targeted enumeration. Which MITRE ATT&CK technique ID covers this activity?  
---
