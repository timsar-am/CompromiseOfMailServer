# PROJECTNAME

Investigating The Compromise of a Mail Server 

## Objective

Today I am going to investigate the compromise of a mail server. The mail filter is down and there are spam emails coming in. A user unknowingly opened a malicious email and deployed malware.  This lab has been provided by CyberDefenders.

### Skills Learned

- Threat Hunting
- Network Forensics 

### Tools Used

- Brim (Zui)
- NetworkMiner
- Wireshark
- Hybrid-Analysis
- VirusTotal
- Thunderbird
- CyberChef
- Linux CLI

## Steps

Question: c41-MTA5-email-01: What is the name of the malicious file? 

I open up the email in Thunderbird and notice a zip file was attached. 

![image](https://github.com/user-attachments/assets/b75c1938-a620-4427-8353-88099d03fe27)

Inside the zip file we find the answer. 460630672421.exe

![image](https://github.com/user-attachments/assets/1f525f54-e0ae-48c5-81a1-c09af6407a96)

Question: c41-MTA5-email-01: What is the name of the trojan family the malware belongs to? (As identified by emerging threats ruleset).  

I open the terminal and run the command sha256sum 460630672421.exe

![image](https://github.com/user-attachments/assets/37e01ba0-73af-4ad5-98cc-30e72da23f75)

I go over to VirusTotal with the hash. I find the answer: upatre

![image](https://github.com/user-attachments/assets/49ae46f6-e96e-4676-a64a-a2c6f77bb6e8)

Question: c41-MTA5-email-02: Multiple streams contain macros in this document. Provide the number of the highest one 

I open the email in Thunderbird and download the file to the desktop. I had to look this up. I will have to run a python script which has been provided. To make it easier I move the script to the Desktop. I run the command python3 oledump.py “Bill Payment_000010818.xls” I find the answer. 20. 

![image](https://github.com/user-attachments/assets/4ec2d196-7644-4713-af18-4011edcd2904)

Question: c41-MTA5-email-02: The Excel macro tried to download a file. Provide the full URL of this file.

I run the command sha256sum “Bill Payment_000010818.xls”

![image](https://github.com/user-attachments/assets/42772c9a-a869-45ee-9ba3-ec4c2ae72363)

I take the hash over to VirusTotal and find the answer.  hxxp[://]advancedgroup[.]net[.]au/~incantin/334g5j76/897i7uxqe[.]exe

![image](https://github.com/user-attachments/assets/2ea39f23-a440-4a2d-a733-61ea83dc063c)

Question: c41-MTA5-email-02: The Excel macro writes a file to the temp folder. Provide the filename.

Once again the answer can be found in VirusTotal. Tghtop.exe

![image](https://github.com/user-attachments/assets/e9a78cbf-59cd-4850-a6a0-2482fb6b7563)

Question: c41-MTA5-email-03: Provide the FQDN used by the attacker to store the login credentials.

I open up the email in Thunderbird. View Source.  No luck. I save the html file attached to the desktop and run a command in the terminal. Cat AmericanExpress.html | grep “http” I get the answer.

![image](https://github.com/user-attachments/assets/ce71d1ad-7b45-4e89-9eb7-8865ae1b285f)

Question: c41-MTA5-email-04: How many FQDNs are present in the malicious js? 

We have a malicious Javascript so I need to open up the email and download the .zip attachment. I extract it and there is a .js file. I open it, copy the script.

![image](https://github.com/user-attachments/assets/0cb2f2f5-049e-4747-ba44-95ab9876c813)

I go over to CyberChef to make this script look a bit better. The output still doesn’t help me.

![image](https://github.com/user-attachments/assets/6569b5a7-f33b-45e1-b3cc-e2ac7c1dddb0)

I take the output from CyberChef to Programiz. The output shows 3 FQDN.

![image](https://github.com/user-attachments/assets/530da383-2aea-4496-8591-02fec31b9499)

Question: c41-MTA5-email-04: What is the name of the object used to handle and read files? 

We already know the answer thanks to the output from previous question. ADODB.Stream. 

![image](https://github.com/user-attachments/assets/8db88706-8e65-4047-ab58-d6c2934ac6fd)

Question: c41-MTA5.pcap: The victim received multiple emails; however, the user opened a single attachment. Provide the attachment filename. 

From the previous questions I already know of 3 malicious domains. kennedy[.]sitoserver[.]com nzvincent[.]com abama[.]org

I go over to Zui and run a simple search for Kennedy. 3 HTTP connections show up.

![image](https://github.com/user-attachments/assets/56fde985-0d48-434a-895d-142fb8665fe1)

From here I determine the victim’s IP address and the attacker. I go over to Wireshark to get more information.

I search for the two IP addresses and there seems to be an awful amount of activity. 

![image](https://github.com/user-attachments/assets/f055daa6-70cb-46a7-92ae-ce3404a35cc2)

Now that I have a bit more information I go back to Brim. We see the connection with kennedy[.]sitoserver[.]com and we know already know the malicious file associated with the email associated with this. Answer is fax000497762.zip

Question: c41-MTA5.pcap: What is the IP address of the victim machine? 

I determined this in the last task. 10.3.66.103

Question: c41-MTA5.pcap: What is the FQDN that hosted the malware? 

![image](https://github.com/user-attachments/assets/60227f70-d513-4225-9c36-7783944a3bde)

Also determined in the last task. Answer is  kennedy[.]sitoserver[.]com

Question: c41-MTA5.pcap: The opened attachment wrote multiple files to the TEMP folder. Provide the name of the first file written to the disk? 

We have the script from previous tasks. Highlighted is the key parts of this script creating a file in temp folder. Name of the file will be 799755 + N. N=1 for the first file and the it will be .exe executable. The answer will be 7997551.exe

![image](https://github.com/user-attachments/assets/9093a2cb-ea62-4bbf-bc1f-ffd10f8a67ba)

Question: c41-MTA5.pcap: One of the written files to the disk has the following md5 hash "35a09d67bee10c6aff48826717680c1c"; Which registry key does this malware check for its existence? 

First, I take the hash and look it up on VirusTotal so I get an idea of what I should be looking for. 
Here we can see the registry actions this malware performs.

![image](https://github.com/user-attachments/assets/077b3735-6599-4e92-9775-f2f7bf07fd37)

Next I search for the MD5 hash on Zui. 

![image](https://github.com/user-attachments/assets/e7faa928-9f46-4ea0-9a2f-41abeb3a026a)

I find the connection in question and see the victim’s IP address. 

![image](https://github.com/user-attachments/assets/c7585d88-2bbf-4482-890e-cb647f8c03ef)

I go to wireshark and run the victims IP address with the port I found in Zui.

![image](https://github.com/user-attachments/assets/d106ef54-3757-4c30-9ec8-8676aca8c37e)

I take notice of the packet with the largest payload. I go over to File> Export Object> HTTP and filter for the malicious domain determined in previous tasks.

![image](https://github.com/user-attachments/assets/3a5cdfd8-fb04-446e-a27a-d04da4f9714a)

There it is packet 1480 with the largest payload. I save it to Desktop as Packet1480. I open the terminal and run command strings Packet1480 I search for interface to find the answer. 9a83a958-b859-11d1-aa90-00aa00ba3258

![image](https://github.com/user-attachments/assets/0e109e1e-b913-4896-8979-cfecbfd75641)

I also see it was noted on VirusTotal.

![image](https://github.com/user-attachments/assets/b48a165e-49db-401c-b44e-e700a0fa2097)

Question: c41-MTA5.pcap: One of the written files to the disk has the following md5 hash "e2fc96114e61288fc413118327c76d93" sent an HTTP post request to "upload.php" page. Provide the webserver IP. (IP is not in PCAP) 

Here I already know I won’t find the answer in Wireshark so I will have to use a combination of Zui,Wireshark and VirusTotal.

Same step as the previous task. Since I already exported the file from packet 1480 I take note of the 2nd largest payload packet 571.

![image](https://github.com/user-attachments/assets/9f6ead05-5e06-4169-867a-1c8ae9e34426)

I export the file and save to my Desktop as Packet571. I open the terminal to determine the sha256 hash.

![image](https://github.com/user-attachments/assets/6cb36d97-1da7-48c3-8b4f-e5d09704e604)

In VirusTotal under the relations tab we see the contacted IP addresses. The answer is the one with the most detections. 78[.]24[.]220[.]229

![image](https://github.com/user-attachments/assets/1f606cd4-246c-47aa-b901-0c84a5871a25)

Question: c41-MTA5.pcap: The malware initiated callback traffic after the infection. Provide the IP of the destination server. 

There would be a lot of traffic with this IP so I look up IP statistics in Wireshark. I notice the below IP address.

![image](https://github.com/user-attachments/assets/b10fdb67-9d34-43f0-8f58-2458df54638a)

I go over to Zui and notice an alert generated “possibly Unwanted Program Detected. The answer is 109[.]68[.]191[.]31

![image](https://github.com/user-attachments/assets/f9d88fde-9260-4681-a145-3c0c13e3558a)













