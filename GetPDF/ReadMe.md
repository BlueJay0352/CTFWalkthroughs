
CyberDefenders CTF Lab GetPDF 


https://cyberdefenders.org/blueteam-ctf-challenges/getpdf/

### Scenario
  
  The network traffic is captured in lala.pcap contains network traffic related to a typical malicious PDF file attack, in which an unsuspecting user opens a compromised web page, which redirects the user’s web browser to a URL of a malicious PDF file. As the PDF plug-in of the browser opens the PDF, the unpatched version of Adobe Acrobat Reader is exploited and, as a result, downloads and silently installs malware on the user’s machine.

### Machine Environment 
- Windows 10 FLARE-VM
- Wireshark
- PDF-parser
	
	
### 1.  How many URL path(s) are involved in this incident? 
---
Extract the 47-GetPDF.rar file and open the lala.pcap in WireShark.

Statistics -> HTTP -> Requests
![image](https://github.com/user-attachments/assets/79b96b47-26a3-471d-bc7a-3dd92e9750a1)



This shows the unique HTTP requests in the pcap file.  

![image](https://github.com/user-attachments/assets/c5e00f27-d3af-42af-8f2b-efd09fb54e25)



### 2.  What is the URL which contains the JS code?
---

Follow the HTTP conversation in WireShark beginning with the first GET request.  This link contains obfuscated JavaScript (JS) which we need to analyze further.

![image](https://github.com/user-attachments/assets/8a4eb14a-1c18-4d52-903e-73d355ef24bd)


### 3.  What is the URL hidden in the JS code?
---

Copy the script and format it to make it readable - I used https://beautifier.io/ but you can also use https://de4js.kshift.me/

Using https://playcode.io you can run parts of the script to determine behavior.  

A var called “GaDemee” stands out as an easy var to decode using console.log.  It is the eval() method and we know JavaScript uses this to run variables or statements.

![image](https://github.com/user-attachments/assets/c1e33381-432c-492a-8a3d-af0fbf420771)


The end of the script runs the var “ZeJexn” using the eval() method we decoded in the previous step.  Logging that var to console we can observe what eval() is executing. 

![image](https://github.com/user-attachments/assets/3e04e66e-173f-4a9d-9c72-e1c270e5224b)



### 4.  What is the MD5 hash of the PDF file contained in the packet?
---

Displays the file retrieved from the “getpdf.php” URL

![image](https://github.com/user-attachments/assets/e224bad6-0685-4468-a859-825fdff70cb9)


The PDF file can be extracted using WireShark.  
File -> Export Objects -> HTTP

![image](https://github.com/user-attachments/assets/dc5e97d2-15b9-45dd-b76a-e15f7393400a)


Retrieve the hash

![image](https://github.com/user-attachments/assets/2548083f-0251-4c5c-9cc2-3a6f62bcded2)


### 5.  How many object(s) are contained inside the PDF file?
---

FLARE-VM for Windows contain PDF tools that can display the objects. 

 ![image](https://github.com/user-attachments/assets/a80d0e17-5df5-4b59-8999-871482f22d92)


### 6.  How many filtering schemes are used for the object streams?
---

Run pdf-parser.py and grep for “filter” to list the filtering schemes.

![image](https://github.com/user-attachments/assets/032d31e6-8073-4f2d-ac71-4037feb1b4c9)


### 7.  What is the number of the 'object stream' that might contain malicious JS code?
---
