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

The lab points us in the direction of a WinRaR file (Preliminary analysis by the IT department has identified a potential compromise linked to an exploited vulnerability in **WinRAR** software)

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

Searching https://www.cvedetails.com for “winrar” yields many results but looking at each description you can determine the one relating to file and folder sharing the same name.

### 4.  In examining the downloaded archive, you noticed a file in with an odd extension indicating it might be malicious. What is the name of this file?
---

File within the .rar archive with the double extension.

### 5.  What is the URL used by the attacker to download the second stage of malware?
---

Using **Strings** to find any obvious urls in the file.

*strings 'SANS SEC401.pdf.cmd' | findstr "\\//"*

![image](https://github.com/user-attachments/assets/b797eab8-4a1e-487a-8bd5-3ec2037f96a1)

---

Before moving on, the following questions require dynamic analysis be done to observe the behavior of the sample.  Since this is an encoded batch file and the easiest way is to execute, we will employ CMDWatcher (https://www.kahusecurity.com/tools.html)  This will enable us to step through the program everytime the file calls **cmd.exe**.  


Start **CMDWatcher** then execute the sample.  Note: you will have to click "Resume Process" twice until a black command window opens which is the first command from the sample.  Click "Kill Process" for the remaining processes.    

![image](https://github.com/user-attachments/assets/39f90657-b90d-405a-88d3-f49718bc8a6a)

![image](https://github.com/user-attachments/assets/171a0566-6c2c-4df7-b4c7-f0ed40eede59)

![image](https://github.com/user-attachments/assets/83189394-b075-4abe-9995-199e8e218194)


### 6.  To further understand how attackers cover their tracks, identify the script they used to tamper with the event logs. What is the script name?
---

If the event logs were erased we can investigate the Windows Event Log Security channel and filter for *eventID=1102*

![image](https://github.com/user-attachments/assets/b121e836-cc68-4c2f-9aa8-5610423d8325)

Sure enough there it is - something cleared the logs at 11:38:01 PM 2/2/2024.  Since the question mentions a script was used, lets look at the Powershell event log channel at that particular time.

![image](https://github.com/user-attachments/assets/6c57ee4a-859f-4a14-941b-dd886bc65723)

BINGO!  There's the script which cleared the logs.


### 7.  What is the UTC timestamp for when the script that tampered with event logs was run?
---

Using the same event log from question 6 lets look at the timestamp in the **Details** tab.  Note the “Z” at the end of the "TimeCreated SystemTime" which denotes the Zulu timezone which is equal to UTC.

![image](https://github.com/user-attachments/assets/2b0b6739-147c-46e1-a2a9-b4f48b8fbf14)

### 8.  What is the command used by the attacker for persistance?
---

Using the output from **CMDWatcher** we can see it creates (/create) a scheduled task named (/tn) “whoisthebaba” configured to run every 3 minutes (/sc minute /mo 3) running the task (/tr) run.bat with highest privleges.

![image](https://github.com/user-attachments/assets/64d3393c-acac-4066-9aa6-88547e2258e1)

Lets investigate *run.bat* file with **CMDWatcher**.  It executes *run.ps1* which is located in the C:\Windows\Temp folder.  This file will prove useful for answering the next question.

![image](https://github.com/user-attachments/assets/f4878835-dd37-4d25-b30a-8295da2ec8df)


### 9.  What is the full path of the file storing the data collected by one of the attacker's tools in preparation for data exfiltration?

Continuing with the analysis of *run.ps1* lets open it with a text editor.

![image](https://github.com/user-attachments/assets/bd34f0f5-2663-49d2-8cfa-2edaeca74c00)

Looks like Base64 which gets reversed then decoded into var $LOAdCode.  Var $PWN assigns the command *invoke-expression* then creates an alias and assigns the value to $PWN which runs $LOAdCode

