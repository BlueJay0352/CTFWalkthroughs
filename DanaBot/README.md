CyberDefenders CTF Lab DanBot
---


https://cyberdefenders.org/blueteam-ctf-challenges/danabot/

#### Scenario

Our SOC team detected suspicious activity in the network traffic. A machine has been compromised, and company information that should not have been there has now been stolen. It’s up to you to determine what happened and what data was taken.



#### Machine Environment 

- WireShark
- VirusTotal and Any.run


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
