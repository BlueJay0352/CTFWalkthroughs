CyberDefenders CTF Lab SpottedInTheWild
---


https://cyberdefenders.org/blueteam-ctf-challenges/spottedinthewild
#### Scenario

You are part of the incident response team at FinTrust Bank. This morning, the network monitoring system flagged unusual outbound traffic patterns from several workstations. Preliminary analysis by the IT department has identified a potential compromise linked to an exploited vulnerability in WinRAR software.
As an incident responder, your task is to investigate this compromised workstation to understand the scope of the breach, identify the malware, and trace its activities within the network.



#### Machine Environment 

- Windows 10 with Flare running in VMware workstation https://github.com/mandiant/flare-vm
- Eric Zimmermans tools https://ericzimmerman.github.io/#!index.md 
- Arsenal Image Mounter https://arsenalrecon.com/downloads


---
Extract the lab file and mount the .vhd 
 
![image](https://github.com/user-attachments/assets/b24aafa1-dcf7-4059-b01b-360854b49f8b)

Mounted Windows file system
![image](https://github.com/user-attachments/assets/f0e87a3b-a78e-4bc4-8743-d2bcb9070dbe)


### 1.  Which application was used to download the malicious file?
---

Now where to start and what should we be looking for? The lab points us in the direction of a WinRaR file (Preliminary analysis by the IT department has identified a potential compromise linked to an exploited vulnerability in **WinRAR** software)

I list all the .rar files so see where this possible rar file is located.  

*Get-ChildItem E:\ -Recurse -file -name -filter "*.rar"*

![image](https://github.com/user-attachments/assets/cfb73d01-6be1-4a3f-be01-7b40c7521f66)

### 2.  What is the UTC timestamp for when the suspicious file was first downloaded?
---
We can assume the suspicious file is what we found from the previous question **SANS SEC401.rar**
Lets look at the Windows "$MFT" file which is a database that tracks all object changes on NTFS systems.  This should give us our timestamp for when it was created on the file system.

Using Zimmermans's **MFTECmd.exe** program we can extract MFT entries to a CSV and read them with Timeline Explorer

*./MFTECmd.exe -f 'E:\VSS1\$MFT' --csv "C:\Temp\out"*

![image](https://github.com/user-attachments/assets/0430052f-4823-4280-b562-8b1d5b84791e)

Using **Timeline Explorer** open the MFT CSV file.
Search for the “SANS SEC401" file and note the column “Created”

![image](https://github.com/user-attachments/assets/15a06fc1-4c2b-4e12-bccd-3843b48da0cd)


### 3.  What is the CVE identifier of the vulnerability used in this attack?
---


### 4.  What is the URL used by the attacker to download the second stage of malware?
---

Using **Strings** to find any obvious urls in the file.

*strings 'SANS SEC401.pdf.cmd' | findstr "\//"*

![image](https://github.com/user-attachments/assets/b797eab8-4a1e-487a-8bd5-3ec2037f96a1)


