CyberDefenders CTF Lab FakeGPT
---

https://cyberdefenders.org/blueteam-ctf-challenges/fakegpt/

#### Scenario

Your cybersecurity team has been alerted to suspicious activity on your organization's network. Several employees reported unusual behavior in their browsers after installing what they believed to be a helpful browser extension named "ChatGPT". However, strange things started happening: accounts were being compromised, and sensitive information appeared to be leaking.


#### Machine Environment 

- Windows 10 running in VMware workstation
- Flare Windows Install https://github.com/mandiant/flare-vm


### 1.  Which encoding method does the browser extension use to obscure target URLs, making them more difficult to detect during analysis?

	Base64

### 2.  Which website does the extension monitor for data theft, targeting user accounts to steal sensitive information?

	www.facebook.com


### 3.  
Which type of HTML element is utilized by the extension to send stolen data?

![image](https://github.com/user-attachments/assets/995d4e95-2ba4-4476-8bbc-a998c6e2a8aa)

	
	<img>
	

### 4.  What is the first specific condition in the code that triggers the extension to deactivate itself?

	navigator.plugins.length === 0


### 5.  Which event does the extension capture to track user input submitted through forms?

	submit

### 6.  Which API or method does the extension use to capture and monitor user keystrokes?

	keydown


### 7.  What is the domain where the extension transmits the exfiltrated data?

	Mo.Elshaheedy.com

### 8.  Which function in the code is used to exfiltrate user credentials, including the username and password?

	exfiltrateCredentials(username, password)


### 9.  Which encryption algorithm is applied to secure the data before sending?

	AES

### 10.  What critical browser API does the extension use to access and manipulate cookies?




