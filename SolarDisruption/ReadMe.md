CyberDefenders CTF Lab SolarDisruption
---

https://cyberdefenders.org/blueteam-ctf-challenges/solardisruption/

#### Scenario

You are a cybersecurity analyst working in the Security Operations Center (SOC) at AetherCore Technologies, a company that provides engineering and manufacturing services for electronic products, including industrial solar energy systems. AetherCore relies on programmable logic controllers (PLCs) to manage the solar panel systems in its facilities. These systems are critical for maintaining the company’s solar energy production and efficient operation.
Recently, AetherCore’s engineering team reported a significant disruption in their solar panel operations. Several panels have gone offline, and attempts to remotely restart them have failed. The incident occurred shortly after 16:10, following a spike in network activity. Initial hardware checks found no physical issues with the panels or the PLCs.
You and your team have been tasked with investigating whether this outage was caused by a cybersecurity incident. There is suspicion that an insider threat may be involved, using their access to the network to manipulate the PLCs and disrupt solar panel operations.


#### Machine Environment 

VM provided by CyberDefenders

WireShark

Zui Brim

### 1.  In the provided packet capture, several protocols are present, but one stands out for its popularity in Industrial Control Systems and Programmable Logic Controllers (PLCs). It is used to transmit data between devices like PLCs and sensors, allowing real-time monitoring and process control. What is the name of this protocol?
---

Protocol hierarchy and TCP Conversations in WireShark can confirm the port in question.
 TCP port 502 is a well known PLC port used for sensor communication.

<img width="1107" height="209" alt="image" src="https://github.com/user-attachments/assets/41124c3a-28ed-4560-8340-26b50b487122" />

<img width="941" height="338" alt="image" src="https://github.com/user-attachments/assets/fa2c5e91-5088-483c-aadc-6bdf15187e31" />


### 2.  Some analysis tools offer a histogram view of the packet capture, which visualizes network activity over time and helps analysts identify patterns, trends, or anomalies such as traffic spikes. Determine the duration of the traffic spike in the packet capture, rounding the result to the nearest second, and provide your answer in seconds.
---

Using Brim you can select the part of the histogram that stands out as a traffic spike.  

<img width="1341" height="648" alt="image" src="https://github.com/user-attachments/assets/3e060272-bba1-4e84-8b4b-e7423955abca" />


### 3.  Traffic spikes are often linked to scanning activities by adversaries, which become evident when a single host IP generates a large number of requests within a short period. What is the IP address responsible for the traffic spike?
---

 Using Brim again focus on the traffic spike and craft a query that show source IP scanning multiple destination ports.

 <img width="700" height="140" alt="image" src="https://github.com/user-attachments/assets/4784be76-526e-4fa4-b7c4-82f5132ae331" />

<img width="979" height="415" alt="image" src="https://github.com/user-attachments/assets/19b50177-11c5-4990-bd13-e14f56ef5f2e" />

Wireshark also confirms by showing this IP as having a high burst rate.

Statistics -> IPv4 Statistics -> Source and Destination Addresses
Sort the “Burst Rate” column to see possible spikey scanning activity

<img width="854" height="163" alt="image" src="https://github.com/user-attachments/assets/55227854-ded6-4268-996d-83266eecfd55" />

### 4.  After identifying the attacker's IP address, the next step is to determine which network hosts the attacker interacted with, a process known as host discovery. This involves analyzing the traffic to see how many devices or systems the attacker communicated with on the network. Based on the packet capture analysis, how many hosts did the attacker discover?
---

This question requires a query of all of the destination (id.resp.h) addresses sourced from our attacker IP (192.168.228.203)
 
 id.orig_h==192.168.228.203 | cut id.resp_h | sort | uniq

<img width="613" height="603" alt="image" src="https://github.com/user-attachments/assets/832f8b39-e713-402e-a57e-e2c7e069759f" />

We are concerned only with the PLC network 192.168.228.0/24.

### 5.  After completing host discovery, adversaries typically conduct a port scan to identify potential vulnerabilities and determine their attack surface. How many ports did the attacker scan on each of the discovered hosts?
---

The query below shows all the ports the attacker scanned on one of the hosts.

id.orig_h==192.168.228.203 AND id.resp_h==192.168.228.139 | cut id.resp_p

 <img width="1450" height="580" alt="image" src="https://github.com/user-attachments/assets/cc1cf476-7553-4394-addf-81c15a45bc38" />

### 6.  Now that we have confirmed the attacker's IP and intentions, let's begin analyzing their actions. Which HTTP host did the attacker first interact with after completing their enumeration?
---

The query below shows the attackers http requests and ports.  Remember, we are only concerned with the local IP subnet (192.168.228.0/24)

_path=="http" AND id.orig_h==192.168.228.203 | cut id.resp_h, id.resp_p

<img width="797" height="571" alt="image" src="https://github.com/user-attachments/assets/fbf0260e-eaac-4346-959c-dcd87259fedc" />

### 7.  The first host the attacker interacted with is a PLC (Programmable Logic Controller) device. To understand the attack better, it's important to identify the specific PLC runtime being used on this host, as this could give insights into the attack methods and vulnerabilities. What is the name of the PLC runtime used by this host?
---

A PLC runtime is the software that runs on a PLC device or a PC that executes the logic of a programmable logic controller program.  Using the answer from question 6 we can discover more about port 8080 in this enviornment.   

We can query http requests sourcing from our attacker IP (192.168.228.203) destined for port 8080.
_path=="http" AND id.orig_h==192.168.228.203 AND id.resp_p==8080

This shows uri's which give away the name of the PLC runtime being used. 

<img width="828" height="455" alt="image" src="https://github.com/user-attachments/assets/3572a586-f0f4-4c95-8da8-f419f463310a" />

### 8.  The attacker appears to have successfully logged into the PLC's configuration webserver. What credentials did they use to gain access?
---

Using WireShark lets query for http POST's with the destination IP of our victim (192.168.228.138)
Dig into the http packet form URL Encoded data

ip.dst == 192.168.228.138 && http.request.method == "POST"

 <img width="800" height="649" alt="image" src="https://github.com/user-attachments/assets/3b254373-c007-4827-b391-7c6f951d3f0e" />

 ### 9.  According to the incident report, the credentials for the OpenPLC configuration webserver were changed by the attacker. Can you identify the new password that the attacker set?
---

Using the same WireShark query from question 8, select the 2nd packet (edit-user). 

<img width="971" height="699" alt="image" src="https://github.com/user-attachments/assets/7efd0fa8-d29f-4cd9-bbda-cb6004f76583" />

The MIME data can be viewed as text showing the plaintext password that was changed.

<img width="947" height="418" alt="image" src="https://github.com/user-attachments/assets/2f5ebaec-0307-4dba-a7d0-a6267a14844b" />

### 10.  The PLC's configuration webserver enables engineers to configure and monitor various parameters of the PLC device. This access can also allow an attacker to identify the I/O points or the registers/coils numbers and their mappings. How many I/O points were in use on the PLC?
---

Within WireShark
File -> Export Objects -> HTTP
Text Filter: 192.168.228.138

The question asks for I/O being monitored so we can export the monitoring http file.

<img width="829" height="193" alt="image" src="https://github.com/user-attachments/assets/058491a6-f81c-4f0a-a0b2-0bbf7dc81296" />

Open it with your browser

<img width="1593" height="666" alt="image" src="https://github.com/user-attachments/assets/ec70393d-5c56-45c3-852b-fac00596caa5" />

### 11.  Following the identification of the I/O points, what is the Modbus location of the Emergency Stop coil on the PLC?
---

Use the monitoring page from question 10 to find the location.


### 12.  The attacker seems to have sent multiple Modbus requests using the "Write Single Coil" command, specifically targeting the emergency stop coils. This repeated activation of the emergency stop likely caused our system's downtime. Based on the information provided, can you calculate the total duration of the downtime in seconds (rounded) caused by the attacker?
---

WireShark Filter
ip.addr==192.168.228.203 && ip.addr==192.168.228.138 && tcp.port==502


Start time

<img width="1222" height="92" alt="image" src="https://github.com/user-attachments/assets/5ee59c63-bd81-4098-b5c1-59ae72b0aa49" />

End time

<img width="1224" height="121" alt="image" src="https://github.com/user-attachments/assets/4c568e88-16fa-4867-946e-70aa52a420c8" />

Zui Brim Filter
id.orig_h==192.168.228.203 AND id.resp_h==192.168.228.138 | func=="WRITE_SINGLE_COIL"





