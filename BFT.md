Scenario:

In this challenge, a victim’s device has been compromised with malware, and we need to investigate what happened. The twist? We’re only given access to the Master File Table from the device. Fortunately, this is a robust forensic artifact that contains an entry for every file on the system — including size, timestamps, permissions, and more!

Artifacts:

	- $MFT file


Q) Simon Stark was targeted by attackers on February 13. He downloaded a ZIP file from a link received in an email. What was the name of the ZIP file he downloaded from the link?

Open Terminal and run the tool using the syntax below:

chainsaw dump \$MFT --json > mft.json

Filter the output with .zip extension

**jq '.[] | select(.FullPath and (.FullPath | contains(".zip")))' mft.json**

Based from the results above, seems there are only 2 .zip files inside Simon Downloads directory at 13th February 2024.
**invoice.zip and Stage-20240213T093324Z-001.zip**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/94b3edbe-43e9-4374-bc5b-d15fcd1b225b" />
<img width="1920" height="1080" alt="1 1" src="https://github.com/user-attachments/assets/8cd25706-7eb9-4d7c-8a40-b8ca36e1969e" />

**Answer: Stage-20240213T093324Z-001.zip**

Q) Examine the Zone Identifier contents for the initially downloaded ZIP file. This field reveals the HostUrl from where the file was downloaded, serving as a valuable Indicator of Compromise (IOC) in our investigation/analysis. What is the full Host URL from where this ZIP file was downloaded?

Next, we need to examine the Zone Identifier for the downloaded ZIP file to identify the URL it came from.

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/5d35d7fb-b60b-4ea5-a557-0d9e74901066" />
<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/c0f44110-a33d-4b2d-b9bc-fd77ecfd96e2" />


**Answer: https://storage.googleapis.com/drive-bulk-export-anonymous/20240213T093324.039Z/4133399871716478688/a40aecd0-1cf3-4f88-b55a-e188d5c1c04f/1/c277a8b4-afa9-4d34-b8ca-e1eb5e5f983c?authuser**

Q) What is the full path and name of the malicious file that executed malicious code and connected to a C2 server?

**jq '.[] | select(.FullPath and (.FullPath | contains("invoice")))' mft.json**

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/5c6c845c-5f3e-4f92-a7c5-938afb3df68f" />
<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/c7392a41-388a-487c-a8ba-e4c4f33ce345" />


Based from the result above, we can see there is a C2 IP and it's port which are as the web server to download a file.

**Answer: Users/simon.stark/Downloads/Stage-20240213T093324Z-001/Stage/invoice/invoices/invoice.bat**

Q) Analyze the $Created0x30 timestamp for the previously identified file. When was this file created on disk?

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/974eed43-19dd-474e-bf84-acf76716b313" />


**Answer: FileNameCreated: 2024-02-13 16:38:39**

Q) Finding the hex offset of an MFT record is beneficial in many investigative scenarios. Find the hex offset of the stager file from Question 3.

Find the Entry Number value for the file. Multiply that number by 1024 and convert it to hex.

23436 * 1024 --------> result to hex

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/94618986-1b87-43a7-935e-cf2e639b7265" />


**Answer: 16E3000**

Q) Each MFT record is 1024 bytes in size. If a file on disk has smaller size than 1024 bytes, they can be stored directly on MFT File itself. These are called MFT Resident files. During Windows File system Investigation, its crucial to look for any malicious/suspicious files that may be resident in MFT. This way we can find contents of malicious files/scripts. Find the contents of The malicious stager identified in Question3 and answer with the C2 IP and port.

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/9025741b-ad1a-4016-a185-b793f903b7ac" />


**Answer: 43.204.110.203:6666**
