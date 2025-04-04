CyberDefenders CTF Lab PsExec Hunt
---

https://cyberdefenders.org/blueteam-ctf-challenges/psexec-hunt/

#### Scenario

Our Intrusion Detection System (IDS) has raised an alert, indicating suspicious lateral movement activity involving the use of PsExec. 
To effectively respond to this incident, your role as a SOC Analyst is to analyze the captured network traffic stored in a PCAP file.


#### Machine Environment 

- Windows 10 running in VMware workstation
- Wireshark


### 1.  In order to effectively trace the attacker's activities within our network, can you determine the IP address of the machine where the attacker initially gained access?
---
I like to begin by looking at the pcap statistics in wireshark to get an idea of how many endpoints are in the conversation.  From there we can sort by bytes sent and received to see the top talkers in 	the pcap.  Also, taking note of which ports are utilized within the conversations will tell us what kind of traffic we need to focus our analysis on.

Wireshark -> Statistics -> Conversations
	
![stats](https://github.com/user-attachments/assets/2c016c62-db22-43d8-acdd-f7bb65ce1fdd)


Looking deeper into our top talker IP 10.0.0.130, we filter for the source packets.
##### *ip.src == 10.0.0.130*
	
 ![sourceip](https://github.com/user-attachments/assets/ea7dd1fd-3b0b-4e60-9c14-ac01b5aa3b3b)

	
Based on this behavior of 10.0.0.130 enumerating the windows shares of 10.0.0.133 we can assume the attacker gained access to 10.0.0.130. 
	
  
### 2.  To fully comprehend the extent of the breach, can you determine the machine's hostname to which the attacker first pivoted?
---
Select frame 126 as this is the beginning of SMB traffic from the attacker to 10.0.0.133 (SALES-PC) ; right click -> Follow TCP Stream

![salespc](https://github.com/user-attachments/assets/8d0588fe-8514-48bf-a21c-8fb3f34cba49)
	
Frame 131 contains the string “S.A.L.E.S.-.P.C” in a SMB2 Session Setup Response to 10.0.0.130.  
	
![ssales](https://github.com/user-attachments/assets/3677dac4-049a-419d-a1cd-14d96559d884)

	
We also see the SMB2 Header Session Id contains the account utilized for SMB2 authentication and the hostname for 10.0.0.130 (HR-PC).
		

### 3.  After identifying the initial entry point, it's crucial to understand how far the attacker has moved laterally within our network. Knowing the username of the account the  attacker used for authentication will give us insights into the extent of the breach. What is the username utilized by the attacker for authentication?
---
Using the info gathered from question 2 and adding the Session Id: Account as a column displays the username 
  
![column](https://github.com/user-attachments/assets/cf1a4a23-6aac-440b-b21e-5f3e4a62e7fd)

### 4.  After figuring out how the attacker moved within our network, we need to know what they did on the target machine. What's the name of the service executable the attacker set up on the target?
---	
It's obvious from scrolling through the pcap, PSEXESVC.exe was transferred to 10.0.0.133 (SALES-PC)  using SMB2.

### 5.  We need to know how the attacker installed the service on the compromised machine to understand the attacker's lateral movement tactics. This can help identify other affected systems. Which network share was used by PsExec to install the service on the target machine?
---
Frame 145 is the response packet for creating PSEXESVC.exe on 10.0.0.133 (SALES-PC) 
	
![share](https://github.com/user-attachments/assets/6f12ac40-82d1-4bc9-a108-8d6b6dab44a2)


### 6. We must identify the network share used to communicate between the two machines. Which network share did PsExec use for communication?
---
Share used for psexec commands.  Ioctl = Input Output Control
	
![control](https://github.com/user-attachments/assets/86401e66-aa26-4b3b-b960-037d1befa04c)


### 7.  Now that we have a clearer picture of the attacker's activities on the compromised machine, it's important to identify any further lateral movement. What is the machine's hostname to which the attacker attempted to pivot within our network?
---
This was tricky so I just guessed the naming convention based on the previous computer hostnames.  
	
- HR-PC
- SALES-PC
	
I filtered for IP packets containing “-PC"
##### *ip contains “-PC”*
		
![attempt](https://github.com/user-attachments/assets/cfcc1cc2-31f3-4ddd-88d1-ff6b6353d496)
	
		
Delving deeper to confirm attempted lateral movement to 10.0.0.131 we can use the following filter 
##### *ip.addr == 10.0.0.131*
		
![logonfail](https://github.com/user-attachments/assets/0de8fbf8-32a7-4242-8455-d2253a765037)

	
 
 **BINGO!**  SMB2 Logon attempt using account jdoe failure and “Marketing-PC” within the HexDump for 10.0.0.131
	

*end*
	
	
