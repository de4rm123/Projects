Scenario:

Alonzo Spotted Weird files on his computer and informed the newly assembled SOC Team. Assessing the situation it is believed a Kerberoasting attack may have occurred in the network. It is your job to confirm the findings by analyzing the provided evidence. 

Artifacts:

  	- Security Logs from the Domain Controller 
	- PowerShell-Operational Logs from the affected workstation 
	- Prefetch Files from the affected workstation

Preparation:

	- chainsaw dump SECURITY-DC.evtx --json > security-DC.json
	- chainsaw dump Powershell-Operational.evtx --json > Powershell-Operational.json
	- PECmd.exe -d .\prefetch --json output

Q) Analyzing Domain Controller Security Logs, can you confirm the UTC date & time when the kerberoasting activity occurred?

**jq '.[] | select(.Event.System.EventID==4769 and .Event.EventData.TicketEncryptionType=="0x17")' security-DC.json**

Find “Ticket Encryption Type: 0x17” and EventID 4769 A Kerberos service ticket was requested, then we will have timestamp and information about service that was targeted along with host IP address of this event

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/dd66055b-2e50-45fc-b50f-0afc27275a85" />


**Answer: 2024-05-21 03:18:09**

Q) What is the Service Name that was targeted?

**jq '.[] | select(.Event.System.EventID==4769 and .Event.EventData.TicketEncryptionType=="0x17")' security-DC.json**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/cd96ff1c-10c9-46ea-a7d9-244631641d4b" />


**Answer:  "ServiceName": "MSSQLService"**

Q) It is really important to identify the Workstation from which this activity occurred. What is the IP Address of the workstation?

**jq '.[] | select(.Event.System.EventID==4769 and .Event.EventData.TicketEncryptionType=="0x17")' security-DC.json**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/8cb63a68-8aa2-4a24-abeb-3aa91a388c46" />


**Answer: "IpAddress": "::ffff:172.17.79.129"**

Q) Now that we have identified the workstation, a triage including PowerShell logs and Prefetch files are provided to you for some deeper insights so we can understand how this activity occurred on the endpoint. What is the name of the file used to Enumerate Active directory objects and possibly find Kerberoastable accounts in the network?

**jq '.[] | select(.Event.System.EventID==4104 and select(.Event.EventData.MessageNumber==1))' Powershell-Operational.json**

Switch to Powershell operation event log and we can focus on Event ID 4104 which is Execute a Remote Command event and we can found a script full path C:\\Users\\alonzo.spire\\Downloads\\powerview.ps1.

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/33b63c82-d9d7-4c7a-807d-899cb9070f4b" />
<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/6e86bad0-e119-4f7a-97b6-8968e7848f87" />


**Answer: powerview.ps1**

Q) When was this script executed? (UTC)

**jq '.[] | select(.Event.System.EventID==4104 and select(.Event.EventData.MessageNumber==1))' Powershell-Operational.json**

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/43f51b45-6f6f-4b3f-b876-be1260c924e3" />


**Answer: 2024-05-21 03:16:32**

Q) What is the full path of the tool used to perform the actual kerberoasting attack?

There are several tool that can be used to perform kerberoasting, but prefetch folder show a tool named Rubeus.exe

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/44811bd5-f118-4bf2-a7e6-d1b15c405eb2" />
<img width="1024" height="768" alt="6" src="https://github.com/user-attachments/assets/747fd6ff-e1c5-4263-a66e-8dfb9cdf604f" />


**Answer: C:\Users\Alonzo.spire\Downloads\Rubeus.exe**

Q) When was the tool executed to dump credentials? (UTC)

<img width="1024" height="768" alt="5" src="https://github.com/user-attachments/assets/3e753272-8ecf-4c97-b9f6-7a3689211c73" />


**Answer: 2024-05-21 03:18:08**
