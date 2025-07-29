CyberDefenders CTF Lab Insider (FTK)
---

https://cyberdefenders.org/blueteam-ctf-challenges/insider/

#### Scenario
After Karen started working for 'TAAUSAI,' she began doing illegal activities inside the company. 'TAAUSAI' hired you as a soc analyst to kick off an investigation on this case.
You acquired a disk image and found that Karen uses Linux OS on her machine. Analyze the disk image of Karen's computer and answer the provided questions.


#### Machine Environment 

- Windows 10 running in VMware workstation
- Flare Windows Install https://github.com/mandiant/flare-vm
- FTK Imager https://www.exterro.com/ftk-product-downloads

### 1.  Which Linux distribution is being used on this machine?
---

Open FTK imager
File -> Add Evidence Item -> Image File -> FirstHack.ad1

Navigate to the /boot folder to find the name of the distro

<img width="940" height="324" alt="image" src="https://github.com/user-attachments/assets/6965d241-f26c-405c-820e-18adef20c0f0" />


### 2.  What is the MD5 hash of the Apache access.log file?
---

In FTK Imager make sure to have Properties enabled for files/folders.



Root -> Var -> Log -> apache2 -> access.log

Look at the lower left for properties info which contain file hash data.



### 3.  It is suspected that a credential dumping tool was downloaded. What is the name of the downloaded file?
---

Browse the Downloads folder
Root -> root -> Downloads



 

### 4.  A super-secret file was created. What is the absolute path to this file?
---

The .bash_history file is usually stored in /root directory.  Observe the commands to find the name of the secret file.




### 5. What program used the file didyouthinkwedmakeiteasy.jpg during its execution?
---

Look through the .bash_history file which shows the execution.




### 6.  What is the third goal from the checklist Karen created?
---

Check often used folders like Documents, /home, or Desktop for user created files.




### 7.  How many times was Apache run?
---

Apache stores their logs at /var/logs/apache2.  All logs files are empty which tells us that Apache was never ran.




### 8.  This machine was used to launch an attack on another. Which file contains the evidence for this?
---

Instead of manually searching through the whole drive for the evidence, we can use the cyberdefenders.org answer field which expects a file with a 4 char in length file extension.



We begin by exporting the full file hash list of the image. 



Save as csv and open in excel.  

Excel PowerQuery is a tool we can use to extract all file extensions and search for length=4.

Delete the MD5 and SHA1 columns.



Select the FileNames column -> Data -> Format Table/Range



Add Column -> Extract -> Last Characters



Enter 5 for the Count which will include the “.” 



Close PowerQuery window and return the excel workbook.  
A new column is created called "Last Characters" which we can filter for "." and select only the extensions with 4 characters.



Filtered listing of files with a 4 character file extension.  



The last file irZLAohL.jpeg stands out from the rest as it's in the /root folder and the only .jpeg in the image.  


### 9.  It is believed that Karen was taunting a fellow computer expert through a bash script within the Documents directory. Who was the expert that Karen was taunting?
---



### 10.  A user executed the su command to gain root access multiple times at 11:26. Who was the user?
---

The auth.log file contains user authentication and searching for the timestamp in question shows the user.  



### 11.  Based on the bash history, what is the current working directory?
---


