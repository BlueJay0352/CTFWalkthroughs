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

<img width="278" height="318" alt="image" src="https://github.com/user-attachments/assets/62f2bc30-1a02-47b0-b5f7-879974ba4ec0" />


Root -> Var -> Log -> apache2 -> access.log

Look at the lower left for properties info which contain file hash data.

<img width="955" height="531" alt="image" src="https://github.com/user-attachments/assets/9d43c1f4-1b1d-43ac-bd65-4e406bba7499" />


### 3.  It is suspected that a credential dumping tool was downloaded. What is the name of the downloaded file?
---

Browse the Downloads folder
Root -> root -> Downloads

<img width="652" height="302" alt="image" src="https://github.com/user-attachments/assets/5e1d0802-2b0e-4f9e-adc4-26b5f0f27686" />


### 4.  A super-secret file was created. What is the absolute path to this file?
---

The .bash_history file is usually stored in /root directory.  Observe the commands to find the name of the secret file.

<img width="1143" height="548" alt="image" src="https://github.com/user-attachments/assets/e3009060-d1b7-4f49-b3e1-cba26f9bf0c5" />


### 5. What program used the file didyouthinkwedmakeiteasy.jpg during its execution?
---

Look through the .bash_history file which shows the execution.

<img width="1026" height="678" alt="image" src="https://github.com/user-attachments/assets/e5b03a7c-7bfe-48fa-8c90-513f52073778" />


### 6.  What is the third goal from the checklist Karen created?
---

Check often used folders like Documents, /home, or Desktop for user created files.

<img width="929" height="435" alt="image" src="https://github.com/user-attachments/assets/938b16b5-51c8-4ff3-8d94-85f87dedea8a" />


### 7.  How many times was Apache run?
---

Apache stores its logs at /var/logs/apache2.  All log files are empty which tells us that Apache was never ran.

<img width="839" height="167" alt="image" src="https://github.com/user-attachments/assets/3aee37ca-2768-4099-9f96-91250226876f" />


### 8.  This machine was used to launch an attack on another. Which file contains the evidence for this?
---

Instead of manually searching through the whole drive for the evidence, we can use the cyberdefenders.org answer field which expects a file with a 4 char in length file extension.

<img width="156" height="57" alt="image" src="https://github.com/user-attachments/assets/5d0250b7-bf75-4960-ab8a-1c8b5b03127e" />

We begin by exporting the full file hash list of the image. 

<img width="653" height="330" alt="image" src="https://github.com/user-attachments/assets/7f3909a4-f17a-43d6-905b-dee63dfaa0db" />

Save as csv and open in excel.  

Excel PowerQuery is a tool we can use to extract all file extensions and search for length=4.

Delete the MD5 and SHA1 columns.

<img width="461" height="410" alt="image" src="https://github.com/user-attachments/assets/8346c8c1-8b31-48c6-a5a1-59f78c49b494" />


Select the FileNames column -> Data -> Format Table/Range

<img width="805" height="359" alt="image" src="https://github.com/user-attachments/assets/66b21026-3d02-43c6-8765-29fef32c3ed5" />

Add Column -> Extract -> Last Characters

<img width="779" height="595" alt="image" src="https://github.com/user-attachments/assets/265c5e57-67d6-47ec-b28e-554d34cebc5b" />

Enter 5 for the Count which will include the “.” 

<img width="461" height="520" alt="image" src="https://github.com/user-attachments/assets/4b541f6c-89dd-4a5e-b5ca-b630201d70e7" />


Close PowerQuery window and return the excel workbook.  
A new column is created called "Last Characters" which we can filter for "." and select only the extensions with 4 characters.

<img width="310" height="533" alt="image" src="https://github.com/user-attachments/assets/e467e591-4bb1-432d-8206-40e11e987fe2" />

Filtered listing of files with a 4 character file extension.  

<img width="1409" height="509" alt="image" src="https://github.com/user-attachments/assets/c486958c-6585-4017-abda-8edadbcd8f1e" />

The last file irZLAohL.jpeg stands out from the rest as it's in the /root folder and the only .jpeg in the image.  


### 9.  It is believed that Karen was taunting a fellow computer expert through a bash script within the Documents directory. Who was the expert that Karen was taunting?
---

<img width="656" height="430" alt="image" src="https://github.com/user-attachments/assets/fbca0583-3bb1-42d7-8c25-212e35ac3f9c" />


### 10.  A user executed the su command to gain root access multiple times at 11:26. Who was the user?
---

The auth.log file contains user authentication and searching for the timestamp in question shows the user.  

<img width="879" height="508" alt="image" src="https://github.com/user-attachments/assets/2632fabe-78b6-437a-a06e-59cdb4c625f8" />


### 11.  Based on the bash history, what is the current working directory?
---

<img width="767" height="430" alt="image" src="https://github.com/user-attachments/assets/a9d90dfd-a849-4d25-886d-8e0b41fd3ceb" />


