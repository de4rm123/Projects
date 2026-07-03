Scenario:

In this challenge, a victim’s device has been compromised with malware, and we need to investigate what happened. The twist? We’re only given access to the Master File Table from the device. Fortunately, this is a robust forensic artifact that contains an entry for every file on the system — including size, timestamps, permissions, and more!

Artifacts:

	- $MFT file

STEPS:
1. In this task we're given a MFT file which we can analyze using MFT Explorer. But I prefer to analyze it by converting the raw MFT file to a .json format.
2. Then analyze the JSON file using **jq** tool.
3. To convert the raw MFT file to .json format, you can either use **chainsaw**. 
	
	**chainsaw dump \$MFT --json > mft.json**

4. The easiest way to identify the zip filename which Simon downloaded at 13th February, we just have to search for .zip and correlate the timestamp.
	
	**jq '.[] | select(.FullPath and (.FullPath | contains(".zip")))' mft.json**


<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/94b3edbe-43e9-4374-bc5b-d15fcd1b225b" />
<img width="1920" height="1080" alt="1 1" src="https://github.com/user-attachments/assets/8cd25706-7eb9-4d7c-8a40-b8ca36e1969e" />

Based from the results above, seems there are only 2 .zip files inside Simon Downloads directory at 13th February 2024.
**invoices.zip and Stage-20240213T093324Z-001.zip**

5. To identify the full HOST URL, We need to examine the Zone Identifier for the downloaded ZIP file to identify the URL it came from.

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/5d35d7fb-b60b-4ea5-a557-0d9e74901066" />
<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/c0f44110-a33d-4b2d-b9bc-fd77ecfd96e2" />

6. Then found out a .bat file inside invoice.zip. This is absolutely indicating a executable file. 
	
	**jq '.[] | select(.FullPath and (.FullPath | contains("invoice")))' mft.json**
   
<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/5c6c845c-5f3e-4f92-a7c5-938afb3df68f" />
<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/c7392a41-388a-487c-a8ba-e4c4f33ce345" />

7. Based from the result above, we can see there is a C2 IP and it's port which are as the web server to download a file.
8. To identify the timestamp for the file creation on disk, simply check **FileNameCreated** column.

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/974eed43-19dd-474e-bf84-acf76716b313" />

9. Another alternative way to review the content of invoice.bat file, simply upload the raw MFT file to a hexeditor then calculate the offset.
	Find the Entry Number value for the file. Multiply that number by 1024 and convert it to hex.
	23436 * 1024 --------> result to hex

<img width="1515" height="789" alt="1" src="https://github.com/user-attachments/assets/94b33645-17cd-4634-b822-fc267e5bea84" />

**Conclusion:**

The MFT is one of the most valuable forensic artifacts on an NTFS filesystem because it records metadata for nearly every file.
Timelines are essential for reconstructing the sequence of events during an incident.
Alternate Data Streams (ADS), especially Zone.Identifier, can reveal the origin of downloaded files.
Resident files may contain their complete contents within the MFT, allowing investigators to recover deleted or otherwise inaccessible scripts.
Malware commonly uses PowerShell download cradles (iwr | iex) and self-deletion techniques to execute payloads while leaving as little evidence as possible.
