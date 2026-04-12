Scenario:

Forela's Network is constantly under attack. The security system raised an alert about an old admin account requesting a ticket from KDC on a domain controller. Inventory shows that this user account is not used as of now so you are ta>

Artifacts:

        - Security.evtx

Preparations:

        - chainsaw dump Security.evtx --json --decode-data-streams > security.json

Q) When did the ASREP Roasting attack occur, and when did the attacker request the Kerberos ticket for the vulnerable user?

**jq '.[] | select(.Event.System.EventID==4768 and .Event.EventData.TargetUserName != "DC01$" and .Event.EventData.TargetUserName != "Administrator")' security.json**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/7db9e5f0-8ca8-4e70-90ad-6c1eab9289ce" />


**Answer: 2024-05-29 06:36:40**

Q) Please confirm the User Account that was targeted by the attacker.

**jq '.[] | select(.Event.System.EventID==4768 and .Event.EventData.TargetUserName != "DC01$" and .Event.EventData.TargetUserName != "Administrator")' security.json**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/a2e60172-599f-4881-b26f-9b74277e6a78" />


**Answer: arthur.kyle**

Q) What was the SID of the account?

**jq '.[] | select(.Event.System.EventID==4768 and .Event.EventData.TargetUserName != "DC01$" and .Event.EventData.TargetUserName != "Administrator")' security.json**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/9b472046-9296-4d5e-bb12-6e2d847fa7a9" />


**Answer: S-1-5-21-3239415629-1862073780-2394361899-1601**

Q) It is crucial to identify the compromised user account and the workstation responsible for this attack. Please list the internal IP address of the compromised asset to assist our threat-hunting team.

**jq '.[] | select(.Event.System.EventID==4768 and .Event.EventData.TargetUserName != "DC01$" and .Event.EventData.TargetUserName != "Administrator")' security.json**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/0e852fb1-843f-4fa9-b059-8312466001f2" />


**Answer: 172.17.79.129**

Q) We do not have any artifacts from the source machine yet. Using the same DC Security logs, can you confirm the user account used to perform the ASREP Roasting attack so we can contain the compromised account/s?

**jq '.[] | select(.Event.EventData.IpAddress == "172.17.79.129")' security.json**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/b283bb89-afd1-494e-9e22-22a158ac7f90" />


**Answer: happy.grunwald**
