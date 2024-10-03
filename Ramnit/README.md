
CyberDefenders CTF Lab Ramnit
---


https://cyberdefenders.org/blueteam-ctf-challenges/ramnit/

#### Scenario

Our intrusion detection system has alerted us to suspicious behavior on a workstation, pointing to a likely malware intrusion. A memory dump of this system has been taken for analysis. Your task is to analyze this dump, trace the malware’s actions, and report key findings. This analysis is critical in understanding the breach and preventing further compromise.



#### Machine Environment 

- Remnux Linux distro running in VMware workstation
- Volatility 3


### 1.  We need to identify the process responsible for this suspicious behavior. What is the name of the suspicious process?
---
Getting network connections is a fast way to see anything suspicious. 
Running the **netstat** and **netscan** plugin in Volatility3 

*python3 vol.py -f ../investigations/ramnit/mem.dmp windows.netstat*

![image](https://github.com/user-attachments/assets/736288ba-8fb2-487d-a561-2356186a54f7)


An executable named “ChromeSetup.exe” has sent a SYN packet and is waiting for the “SYN-ACK” from IP 58.64.204.181 which is not ordinary.  Either the packet got blocked at a firewall or the server is not online.  Below we issue the Volatility3 command netscan while grepping for lines containing “ChromeSetup”

*python3 vol.py -f ../investigations/ramnit/mem.dmp windows.netscan | grep ChromeSetup*

![image](https://github.com/user-attachments/assets/8eb9a8ce-1656-46bb-afbb-306506b443ce)

  
### 2.  To eradicate the malware, what is the exact file path of the process executable?
---
Using the Volatility3 plugin **cmdline** we can see where it was launched from

*python3 vol.py -f ../investigations/ramnit/mem.dmp windows.cmdline
![image](https://github.com/user-attachments/assets/4585bfa7-1c21-4225-a4b8-879e85952f2c)


	
The user Downloads folder is a common path where malware is dropped and launched.  

### 3.  Identifying network connections is crucial for understanding the malware's communication strategy. What is the IP address it attempted to connect to?
---
Referring back to question 1 shows the external IP ChromeSetup.exe attempted to connect.   
  

### 4.  To pinpoint the geographical origin of the attack, which city is associated with the IP address the malware communicated with?
---	
Using IP whois paste in the IP 58.64.204.181 

![image](https://github.com/user-attachments/assets/22617d12-f4aa-47af-b368-3101e3140300)


### 5.  Hashes provide a unique identifier for files, aiding in detecting similar threats across machines. What is the SHA1 hash of the malware's executable?
---
In order to find the hash of the executable we need to dump the files associated with the ChromeSetup.exe process ID (pid).

*python3 vol.py -f ../investigations/ramnit/mem.dmp -o /home/remnux/investigations/ramnit/  windows.dumpfiles --pid 4628*

![image](https://github.com/user-attachments/assets/552a357c-7f1f-471c-a332-b65c9a3b984d)

![image](https://github.com/user-attachments/assets/7f063f7d-88d0-492b-95ac-de8baa02b638)


Get the sha1 hash

![image](https://github.com/user-attachments/assets/f605f7c1-8788-4bce-bfb3-eaaf192a26e4)



### 6. Understanding the malware's development timeline can offer insights into its deployment. What is the compilation UTC timestamp of the malware?
---
Using the hash value found we can look up the malware details from virustotal.com

![image](https://github.com/user-attachments/assets/9c927395-3b38-40fa-ac97-f666af6d5df8)

	

### 7.  Identifying domains involved with this malware helps in blocking future malicious communications and identifying current possible communications with that domain in our network. Can you provide the domain related to the malware?
---
Using VirusTotals behaviour page for the malware sample shows the domain.  

![image](https://github.com/user-attachments/assets/32d69877-7e87-430b-8262-0fc781cfceca)

