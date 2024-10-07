CyberDefenders CTF Lab SpottedInTheWild
---


https://cyberdefenders.org/blueteam-ctf-challenges/ramnit/

#### Scenario

You are part of the incident response team at FinTrust Bank. This morning, the network monitoring system flagged unusual outbound traffic patterns from several workstations. Preliminary analysis by the IT department has identified a potential compromise linked to an exploited vulnerability in WinRAR software.
As an incident responder, your task is to investigate this compromised workstation to understand the scope of the breach, identify the malware, and trace its activities within the network.



#### Machine Environment 

- Windows 10 with Flare running in VMware workstation https://github.com/mandiant/flare-vm
- Eric Zimmermans tools https://ericzimmerman.github.io/#!index.md 
- Arsenal Image Mounter https://arsenalrecon.com/downloads




Extract file and mount the .vhd 
	-Mount options (Disk device, Read only)



Mounted Windows file system




Now where to start and what should we be looking for? The lab points us in the direction of a WinRaR file (network monitoring system flagged unusual outbound traffic patterns from several workstations. Preliminary analysis by the IT department has identified a potential compromise linked to an exploited vulnerability in WinRAR software)

I list all the .rar files so we can see where this possible rar file is located.  

Get-ChildItem -Path "E:\" -Recurse -file -name -force | findstr  ".rar"



Question 1: Which application was used to download the malicious file?  Answer: Telegram
