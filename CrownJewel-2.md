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

1. From the scenario, the attacker previously used vssadmin to create shadow copies for dumping NTDS.dit. To investigate related activity, we can review Event ID 7036 in the System log, which records when a Windows service changes state

   **jq '.[] | select(.Event.System.EventID==7036 and (.Event.EventData.param1 | contains("Shadow")))' system.json**

   <img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/5c2adb34-f15d-4256-93ca-035cab0fe9e0" />

2. By analyzing Event ID 325 in the Application log and correlating its timestamp with other forensic artifacts,we can identify the full path of the dumped NTDS file and the timestamp.

   **jq '.[] | select(.Event.System.EventID=325 and (.Event.System.TimeCreated_attributes.SystemTime | contains("2024-05-15T05:39")))' application.json**

   <img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/cff2865d-bb91-4142-961a-29d520381bf0" />

3. For determining the dumped database considered completed and ready for use, we check the EventID 327 in application log.

   **jq '.[] | select(.Event.System.EventID=327)' application.json**

   <img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/1cdd5c9a-1b18-4f5a-8042-b028b8ab3357" />

4. When ntdsutil.exe is used to dump the database, it enumerates certain user groups to validate the privileges of the account being used. To identify this activity, we can filter events using the full process path C:\Windows\System32\ntdsutil.exe.

   **jq '.[] | select(.Event.EventData.CallerProcessName == "C:\\Windows\\System32\\ntdsutil.exe")' security.json**

   <img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/09c7f5d0-3e5b-4b20-b9e9-8a31926b0aad" />
   <img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/dcfbf471-38fa-48d3-a1b9-d3f665b9f934" />

5. To find Login Time for the malicious Session, we can use LogonID from previous screen and eventid 5379. It indicates that a user has accessed stored credentials such as saved passwords, web credentials, or domain authentication secrets stored in Windows Credential Manager.

   **jq '.[] | select(.Event.EventData.SubjectLogonId == "0x8de3d" and .Event.EventData.SubjectUserName == "Administrator" and .Event.System.EventID == 5379)' security.json**

   <img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/c6b64109-331d-415f-8291-7705c19ca628" />

Conclusion:

The incident demonstrates a critical domain-level breach where the attacker obtained Domain Admin privileges and systematically targeted the Active Directory database. Immediate remediation actions would require full credential rotation, isolation of affected systems, and a comprehensive rebuild of trust in the domain environment.

