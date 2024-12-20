CyberDefenders CTF Lab DanBot
---


https://cyberdefenders.org/blueteam-ctf-challenges/danabot/

#### Scenario

Our SOC team detected suspicious activity in the network traffic. A machine has been compromised, and company information that should not have been there has now been stolen. It’s up to you to determine what happened and what data was taken.



#### Machine Environment 

- WireShark
- VirusTotal


### 1.  What is the malicious file name used for initial access?
---
Lets delve into the conversations and get a quick overview of the type of traffic and endpoints that are present.  Using the “TCP” tab to view ports we see that it's mostly HTTPS traffic with some DNS and HTTP.  Sort the “Port B” column and an interesting thing that stands out is the amount of traffic transferred over port 80 HTTP.  

	Wireshark -> Statistics -> Conversations

![image](https://github.com/user-attachments/assets/dc40587f-0e63-4d94-8399-b7dd6ec59094)



Looking at the beginning of the pcap frame 3 starts a HTTP 3-way handshake to GET login.php

![image](https://github.com/user-attachments/assets/bef79e96-9ccb-4612-8feb-5ba6ce954deb)


Right click frame 6 and go to Follow -> HTTP Stream

![image](https://github.com/user-attachments/assets/72351919-be30-4716-9dc9-609a4c9733c1)


That original GET request for login.php resulted in a javascript file named "allegato_708.js

At a quick glance this looks suspicious, but lets bring it over to a text editor for a closer look.

![image](https://github.com/user-attachments/assets/b2992068-23a9-4955-bca7-2894e99692d0)


After doing some quick formatting to make the code a bit more viewable, we can pick out 3 obvious things to assume this is malicious.
1.  The code is obfuscated with HEX and Base64 encoding.
2.  WScript is called within the code which is used to execute pieces of code.
3.  The JavaScript function decodeURIComponent is present which mean there is an encoded URL within the code.


### 2.  What is the SHA256 hash of the mentioned file?
---

Within WireShark

	File -> Export Objects -> HTTP

Save files login.php and resources.dll since these seem suspicious

![image](https://github.com/user-attachments/assets/179f7f7e-5bd4-4e81-8ecb-eb93194ff75a)


Get the sha265 hash of login.php

![image](https://github.com/user-attachments/assets/9ccfb024-cec0-4441-9c2b-bcfbf2a39ecb)


### 3.  What is the process used to execute the malicious file?
---

Taking that hash we got from question 2, lets go to VirusTotal.com and do some research.
Paste the hash and search.  Go to the “Behavior” tab and scroll down to find “Process and service actions”

![image](https://github.com/user-attachments/assets/ff5e2274-6e3a-45b5-839a-25542332d879)


### 4.  What is the extension of the second malicious file used by the attacker?
---

VirusTotal shows HTTP request which downloads a .dll file.  PCAP also confirm this file was downloaded.

![image](https://github.com/user-attachments/assets/bf1d48ca-6080-44e0-b9d6-519bf5b785fa)

![image](https://github.com/user-attachments/assets/224147e0-7d4e-4a24-a60b-fec19f5b6100)

### 5.  What is the MD5 hash of the second malicious file?
---

Using PowerShell again with Get-FileHash we can specify the algorithm as MD5

![image](https://github.com/user-attachments/assets/23b16797-608f-4907-9e64-90e908ac2fe1)

### 6.  What is the IP address used by the attacker in initial access?
---

Frame 250 is the GET request for resources.dll and we can trace the end of the transfer to frame 9952 (HTTP 200).  Start from there to look for an IP address listed on VirusTotal.

![image](https://github.com/user-attachments/assets/561fbd0a-7c1e-4c76-8682-c30486170d1e)

![image](https://github.com/user-attachments/assets/8cd5fdc0-6477-486a-8cb6-b6d23387d677)


### 7.  What is the last malicious IP address in the PCAP that is known to be used as CnC by DanaBot?
---

Reference the list of contacted IP's from VirusTotal and go through each.

WireShark filter *ip.addr==91.201.67.85*

![image](https://github.com/user-attachments/assets/87381a1e-3256-4432-8b26-1633712fae83)




