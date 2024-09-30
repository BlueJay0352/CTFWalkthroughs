CyberDefenders CTF Lab Ramnit
---


https://cyberdefenders.org/blueteam-ctf-challenges/ramnit/

#### Scenario

Our intrusion detection system has alerted us to suspicious behavior on a workstation, pointing to a likely malware intrusion. A memory dump of this system has been taken for analysis. Your task is to analyze this dump, trace the malware’s actions, and report key findings. This analysis is critical in understanding the breach and preventing further compromise.



#### Machine Environment 

- Windows 10 running in VMware workstation
- Volatility 2 or 3


### 1.  We need to identify the process responsible for this suspicious behavior. What is the name of the suspicious process?
---
I like to begin by looking at the pcap statistics in wireshark to get an idea of how many endpoints are in the conversation.  From there we can sort by bytes sent and received to see the top talkers in 	the pcap.  Also, taking note of which ports are utilized within the conversations will tell us what kind of traffic we need to focus our analysis on.

Wireshark -> Statistics -> Conversations
	
  
### 2.  To eradicate the malware, what is the exact file path of the process executable?
---
Select frame 126 as this is the beginning of SMB traffic from the attacker to 10.0.0.133 (SALES-PC) ; right click -> Follow TCP Stream

		

### 3.  Identifying network connections is crucial for understanding the malware's communication strategy. What is the IP address it attempted to connect to?
---
Using the info gathered from question 2 and adding the Session Id: Account as a column displays the username 
  

### 4.  To pinpoint the geographical origin of the attack, which city is associated with the IP address the malware communicated with?
---	
It's obvious from scrolling through the pcap, PSEXESVC.exe was transferred to 10.0.0.133 (SALES-PC)  using SMB2.

### 5.  Hashes provide a unique identifier for files, aiding in detecting similar threats across machines. What is the SHA1 hash of the malware's executable?
---
Frame 145 is the response packet for creating PSEXESVC.exe on 10.0.0.133 (SALES-PC) 
	

### 6. Understanding the malware's development timeline can offer insights into its deployment. What is the compilation UTC timestamp of the malware?
---
Share used for psexec commands.  Ioctl = Input Output Control
	

### 7.  Identifying domains involved with this malware helps in blocking future malicious communications and identifying current possible communications with that domain in our network. Can you provide the domain related to the malware?
---

I filtered for IP packets containing “-PC"
##### *ip contains “-PC”*
