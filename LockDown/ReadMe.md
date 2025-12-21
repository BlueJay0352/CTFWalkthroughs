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

Set the WireShark filter sourcing from our attacker “ip.src == 10.0.2.4”. 

Statistics -> Conversations TCP tab

This shows the attacker port scanning

<img width="521" height="607" alt="image" src="https://github.com/user-attachments/assets/64214c1b-6143-4e12-b11d-a9b45b99f595" />


### 3. While reviewing the SMB traffic, you observe two consecutive Tree Connect requests that expose the first shares the intruder probes on the IIS host. Which two full UNC paths are accessed?  
---

Filter for smb2 in WireShark

Select the SMB Tree Id and Apply as Column

Sort the column

<img width="884" height="753" alt="image" src="https://github.com/user-attachments/assets/5399e685-273f-4773-a36c-84a135164e8d" />


### 4. Inside the share, the attacker plants a web-accessible payload that will grant remote code execution. What is the filename of the malicious file they uploaded, and what byte length is specified in the corresponding SMB2 Write Request?  
---

WireShark -> File -> Export Objects -> SMB

This shows all the files transfered over SMB and your ability to examine them.

<img width="856" height="660" alt="image" src="https://github.com/user-attachments/assets/7c0ca5e5-2525-4d4c-ae02-2d6aaded81d4" />

### 5. The newly planted shell calls back to the attacker over an uncommon but firewall-friendly port. Which listening port did the attacker use for the reverse shell?
---

Observing the traffic after the payload upload, you can see the victim 10.0.2.15 intiat a 3-way handshake back to the attacker.  

<img width="1459" height="281" alt="image" src="https://github.com/user-attachments/assets/4c771985-33c5-4243-8ece-178460d7b384" />

### 6. Your memory snapshot captures the system’s kernel in situ, providing vital context for the breach. What is the kernel base address in the dump?  
---

Using Volatility 3 in remnux we can retrieve the kernel base address with the “windows.info” option.

<img width="856" height="449" alt="image" src="https://github.com/user-attachments/assets/11aceea6-b20d-453e-87b1-84f9481536af" />

### 7. A trusted service launches an unfamiliar executable residing outside the usual IIS stack, signalling a persistence implant. What is the final full on-disk path of that executable, and which MITRE ATT&CK persistence technique ID corresponds to this behaviour?  
---

sudo /home/remnux/volatility3/vol.py -f memdump.mem windows.pstree

w3wp.exe is a IIS process and it seems legit according to its location in \System32\inetsrv.  It spawned a executable (updatenow.exe) which seems questionable located in a known persistance location. 

<img width="1871" height="233" alt="image" src="https://github.com/user-attachments/assets/d63a4ef5-3801-4e29-9b06-ca1ee6c0dc6f" />

### 8. The reverse shell’s outbound traffic is handled by a built-in Windows process that also spawns the implanted executable. What is the name of this process, and what PID does it run under?  
---

sudo /home/remnux/volatility3/vol.py -f memdump.mem windows.netscan

Using the netscan Volatility 3 option we can output all the network connections active at the time.  


<img width="1268" height="143" alt="image" src="https://github.com/user-attachments/assets/8c9ee8b9-dfa8-4849-a92a-f30896b65af7" />


### 9. Static inspection reveals the binary has been packed to hinder analysis. Which packer was used to obfuscate it? 
---

Using Detect it Easy will show the packer.  

<img width="713" height="525" alt="image" src="https://github.com/user-attachments/assets/25de14b5-cd64-4019-a80f-83edb7c3502f" />


### 10. Threat-intel analysis shows the malware beaconing to its command-and-control host. Which fully qualified domain name (FQDN) does it contact?
---

VirusTotal shows the malicious domain contacted

<img width="970" height="636" alt="image" src="https://github.com/user-attachments/assets/35175ba9-582f-46bc-affd-90c45f6b1f3e" />


### 11. Open-source intel associates that hash with a well-known commodity RAT. To which malware family does the sample belong?  
---

VirusTotal gives insight into the name of the malware family. 

<img width="834" height="628" alt="image" src="https://github.com/user-attachments/assets/1821bbe7-d478-449a-a31a-d65e28a71ffa" />


