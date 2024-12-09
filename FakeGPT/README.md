CyberDefenders CTF Lab FakeGPT
---

https://cyberdefenders.org/blueteam-ctf-challenges/fakegpt/

#### Scenario

Your cybersecurity team has been alerted to suspicious activity on your organization's network. Several employees reported unusual behavior in their browsers after installing what they believed to be a helpful browser extension named "ChatGPT". However, strange things started happening: accounts were being compromised, and sensitive information appeared to be leaking.


#### Machine Environment 

- Windows 10 running in VMware workstation
- Flare Windows Install https://github.com/mandiant/flare-vm


### 1.  Which encoding method does the browser extension use to obscure target URLs, making them more difficult to detect during analysis?
---
Lets first examine the provided files for the case.  Knowing it's a Chrome browser extension we should examine the **manifest.json** file.  This file is the central configuration file defining properties and settings for the Chrome extension.  

![image](https://github.com/user-attachments/assets/110df5f3-9819-4231-bf05-cf9deef0d6ff)

![image](https://github.com/user-attachments/assets/90a0f1a4-1438-4300-8ad6-b324cf211bee)

	
The **background** section defines scripts that runs persistently in the background.  
The **content_scripts** section defines scripts that are run within web pages when the **matches** sub section is matched.

So the loader.js is run persistently in the background while every time a URL is accessed the app.js is injected into the page.  
	
 Looking through app.js there is an obvious function which encodes the payload using a specific method.  CryptoJS.enc.Base64

 ![image](https://github.com/user-attachments/assets/e681dbc7-e914-4977-89a6-2a27cb5492a9)

	

### 2.  Which website does the extension monitor for data theft, targeting user accounts to steal sensitive information?
---
This block of code within **app.js** targets usernames and passwords within submitted web pages.  We can assume the website is the encoded Base64 string.

![image](https://github.com/user-attachments/assets/60084172-6527-47d8-9908-b56072c31083)


The Base64 string can be decoded using CyberChef.

![image](https://github.com/user-attachments/assets/2f688c79-7f6a-40a0-a1e9-7dc2b056980d)


### 3.  Which type of HTML element is utilized by the extension to send stolen data?
---
This function defines the way that data is sent using an HTML element tag

![image](https://github.com/user-attachments/assets/7532460f-a428-41dd-87b6-b0e38d50a1c5)


### 4.  What is the first specific condition in the code that triggers the extension to deactivate itself?
---
The first script to get loaded from manifest.json is **loader.js**.  Examining the first lines of code shows a test to determine if the browser is running in a VM.

![image](https://github.com/user-attachments/assets/057ee8b8-a119-4657-a8c7-606e081069c0)


### 5.  Which event does the extension capture to track user input submitted through forms?
---
This line of JavaScript code is adding an event listener to the **document** object that listens for *submit* events.

![image](https://github.com/user-attachments/assets/832cb318-55f8-4787-8fd2-2a8d5d55c6fe)


### 6.  Which API or method does the extension use to capture and monitor user keystrokes?
---
This code adds an event listener to the **document** object that listen for *keydown* events.

![image](https://github.com/user-attachments/assets/b66e183a-8f86-4c96-8da6-8d9269a7ee68)
	

### 7.  What is the domain where the extension transmits the exfiltrated data?
---
The function **sendToServer** contains the domain where the exfiltrated data is sent.  

![image](https://github.com/user-attachments/assets/6c96518f-8195-439b-8f00-f54bd0720325)


### 8.  Which function in the code is used to exfiltrate user credentials, including the username and password?
---
The name of the function and the code are obvious.

![image](https://github.com/user-attachments/assets/3645bb03-6ab2-4318-8803-75bba30d901f)


### 9.  Which encryption algorithm is applied to secure the data before sending?
---
The **key** variable is a fixed string converted into UTF-8 encoding.

The **iv* variable is the initialization vector which ensures if the same plaintext key are replayed, the resulting ciphertext with differ.

The **encrypted** variable uses the AES algorithm to encrypt the data using the key and iv.

![image](https://github.com/user-attachments/assets/3ed0df98-025f-4170-9835-8bca98ec6f2a)


### 10.  What critical browser API does the extension use to access and manipulate cookies?
---
Since the **manifest.json** contains the “cookies" permission, the extension can query and modify chome cookies.  

![image](https://github.com/user-attachments/assets/00b884a9-fca3-4a62-ab51-164de64b7167)



