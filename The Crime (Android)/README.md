CyberDefenders CTF Lab The Crime (Android)
---

https://cyberdefenders.org/blueteam-ctf-challenges/the-crime/

#### Scenario
We're currently in the midst of a murder investigation, and we've obtained the victim's phone as a key piece of evidence. After conducting interviews with witnesses and those in the victim's inner circle, your objective is to meticulously analyze the information we've gathered and diligently trace the evidence to piece together the sequence of events leading up to the incident.

#### Machine Environment 

- Latest version of CentOS running in VMware workstation
- https://github.com/abrignoni/ALEAPP/tree/main

### 1.  Based on the accounts of the witnesses and individuals close to the victim, it has become clear that the victim was interested in trading. This has led him to invest all of his money and acquire debt. Can you identify the SHA256 of the trading application the victim primarily used on his phone?
---
Download the zip file from cyberdefenders.org and run *aleapp.py* to produce the parsed event output.
![image](https://github.com/user-attachments/assets/26e5200a-61ae-489e-804e-e97ed34e0a1a)

Open the *index.html* file in the reports folder with a web browser.
![image](https://github.com/user-attachments/assets/58574aa1-7d79-455d-93a1-01fd0465e85b)

Now you can browse the data within the phone dump.

Navigate to *Installed Apps* page to get hash values for installed applications
![image](https://github.com/user-attachments/assets/c229e5a6-6db8-449e-9c41-04e37f182cfb)


### 2.  According to the testimony of the victim's best friend, he said, "While we were together, my friend got several calls he avoided. He said he owed the caller a lot of money but couldn't repay now". How much does the victim owe this person?
---

Navigate to the *SMS messages* page.
![image](https://github.com/user-attachments/assets/fa299cdd-7a78-404f-9b97-931003ecd003)

### 3.  What is the name of the person to whom the victim owes money?
---

Cross reference the number (address) in the SMS messages to a contact in the contacts report.
![image](https://github.com/user-attachments/assets/07076e38-6495-49e8-b1be-8fb9f7f524f4)

### 4.  Based on the statement from the victim's family, they said that on September 20, 2023, he departed from his residence without informing anyone of his destination. Where was the victim located at that moment?
---

The *Recent Activity* page displays recent image snapshots and tasks. 

![image](https://github.com/user-attachments/assets/a0021ce3-7604-4fc6-bf14-de133cbbe610)

### 5.  The detective continued his investigation by questioning the hotel lobby. She informed him that the victim had reserved the room for 10 days and had a flight scheduled thereafter. The investigator believes that the victim may have stored his ticket information on his phone. Look for where the victim intended to travel.
---

If the victim saved media in their phone it will be located in the *Emulated Storage Metadata*.  You can search using a word like “ticket” to show any possible plane tickets saved.

![image](https://github.com/user-attachments/assets/af3fa42f-bd0e-490e-ab4c-348421f37b35)

We confirmed there is a plane ticket in the media database external.db.  

Now we have to find where the picture resides in the extracted data dump.  

I preform a *find | grep photo* from the root of the ALEAPP_Report dir.

![image](https://github.com/user-attachments/assets/6b57b437-7e2d-4569-b3db-f1c847aac45b)

The glide_cache contains recently accessed images.  

![image](https://github.com/user-attachments/assets/923ef9b7-7059-4295-a4c8-f15411b00463)

### 6.  After examining the victim's Discord conversations, we discovered he had arranged to meet a friend at a specific location. Can you determine where this meeting was supposed to occur?
---

Navigate to the *Discord Chats* page to view the content of the messages.  
![image](https://github.com/user-attachments/assets/0f6cc33a-771a-48e1-981f-7dfa9d5217bc)

