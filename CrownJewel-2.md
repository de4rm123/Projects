Scenario:

Forela's Domain environment is pure chaos. Just got another alert from the Domain controller of NTDS.dit database being exfiltrated. Just one day prior you responded to an alert on the same domain controller where an attacker dumped NTDS.dit via vssadmin utility. However, you managed to delete the dumped files kick the attacker out of the DC, and restore a clean snapshot. Now they again managed to access DC with a domain admin account with their persistent access in the environment. This time they are abusing ntdsutil to dump the database. Help Forela in these chaotic times!!

Artifacts:

        - APPLICATION.evtx
        - SECURITY.evtx
        - SYSTEM.evtx

Preparations:

        - chainsaw dump APPLICATION.evtx --json > application.json
        - chainsaw dump SECURITY.evtx --json > security.json 
        - chainsaw dump SYSTEM.evtx --json > system.json

Q) When utilizing ntdsutil.exe to dump NTDS on disk, it simultaneously employs the Microsoft Shadow Copy Service. What is the most recent timestamp at which this service entered the running state, signifying the possible initiation of the NTDS dumping process?

Event ID 7036 is an informational log indicating a Windows service changed its state

**jq '.[] | select(.Event.System.EventID==7036 and (.Event.EventData.param1 | contains("Shadow")))' system.json**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/5c2adb34-f15d-4256-93ca-035cab0fe9e0" />


**Answer: 2024-05-15 05:39:55**

Q) Identify the full path of the dumped NTDS file.

**jq '.[] | select(.Event.System.EventID=327)' application.json**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/cff2865d-bb91-4142-961a-29d520381bf0" />


**Answer: C:\Windows\Temp\dump_tmp\Active Directory\ntds.dit**

Q) When was the database dump created on the disk?

**jq '.[] | select(.Event.System.EventID=327)' application.json**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/6eea3851-98b2-4e2e-9707-85981c9cd0b8" />


**Answer: 2024-05-15 05:39:56**

Q) When was the newly dumped database considered complete and ready for use?

**jq '.[] | select(.Event.System.EventID=327)' application.json**

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/1cdd5c9a-1b18-4f5a-8042-b028b8ab3357" />


**Answer: 2024-05-15 05:39:58**

Q) Event logs use event sources to track events coming from different sources. Which event source provides database status data like creation and detachment?

**Answer: ESENT**

Q) When ntdsutil.exe is used to dump the database, it enumerates certain user groups to validate the privileges of the account being used. Which two groups are enumerated by the ntdsutil.exe process? Give the groups in alphabetical order joined by comma space.

**jq '.[] | select(.Event.EventData.CallerProcessName == "C:\\Windows\\System32\\ntdsutil.exe")' security.json**

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/09c7f5d0-3e5b-4b20-b9e9-8a31926b0aad" />
<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/dcfbf471-38fa-48d3-a1b9-d3f665b9f934" />


**Answer: Administrators, Backup Operators**

Q) Now you are tasked to find the Login Time for the malicious Session. Using the Logon ID, find the Time when the user logon session started.

**jq '.[] | select(.Event.EventData.SubjectLogonId == "0x8de3d" and .Event.EventData.SubjectUserName == "Administrator" and .Event.System.EventID == 5379)' security.json**

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/c6b64109-331d-415f-8291-7705c19ca628" />


**Answer: 2024-05-15 05:36:31**
