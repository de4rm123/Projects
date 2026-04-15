Scenario:

Our SIEM alerted us to a suspicious logon event which needs to be looked at immediately . The alert details were that the IP Address and the Source Workstation name were a mismatch .You are provided a network capture and event logs from the surrounding time around the incident timeframe. Corelate the given evidence and report back to your SOC Manager.

Artifacts:

        - ntlmrelay.pcapng
        - Security.evtx

Preparations:

        - chainsaw dump Security.evtx --json > Security.json

Q) What is the IP Address for Forela-Wkstn001?

Wireshark filter protocol **nbns**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/f70af8c4-984b-49e4-9731-4c6282153bbb" />


**Answer: 172.17.79.129**

Q) What is the IP Address for Forela-Wkstn002?

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/7864006d-b954-42ec-a00b-8c5184ec527e" />


**Answer: 172.17.79.136**

Q) What is the username of the account whose hash was stolen by attacker?

EventID log 4624 or filter wireshark traffic with "ntlmssp"

**jq '.[] | select(.Event.System.EventID == 4624 and (.Event.EventData.LogonProcessName | contains("NtLmSsp")))' security.json**

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/add1f479-85b7-4546-9fa3-d8be1ebe6f43" />

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/72db0248-dd7b-4c83-8710-3ec677aefd25" />


**Answer: arthur.kyle**

Q) What is the IP Address of Unknown Device used by the attacker to intercept credentials?

**jq '.[] | select(.Event.System.EventID == 4624 and (.Event.EventData.LogonProcessName | contains("NtLmSsp")))' security.json**

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/27e7c990-d414-46fe-aa2e-e69e8df3edea" />


**Answer: 172.17.79.135**

Q) What was the fileshare navigated by the victim user account?

Wireshark filter "smb2.cmd == 3"

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/db8a7f1a-75f0-496f-8580-8420efd023c4" />


**Answer: \\DC01\Trip**

Q) What is the source port used to logon to target workstation using the compromised account?

**smb2.cmd == 1 && ip.src == 172.17.79.135 && ip.dst == 172.17.79.129**

<img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/40515598-78e4-42bf-8099-9e45ba3d2612" />



**Answer: 40252**

Q) What is the Logon ID for the malicious session?

**jq '.[] | select(.Event.System.EventID == 4624 and (.Event.EventData.LogonProcessName | contains("NtLmSsp")))' security.json**

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/47656d4e-7dff-45ce-b5f0-9dba3f2cf49b" />


**Answer: 0x64a799**


Q) The detection was based on the mismatch of hostname and the assigned IP Address.What is the workstation name and the source IP Address from which the malicious logon occur?

<img width="1920" height="1080" alt="8" src="https://github.com/user-attachments/assets/ca7c88cf-00c2-40ee-b3bb-011a297bf53a" />


**Answer: FORELA-WKSTN002, 172.17.79.135**

Q) At what UTC time did the the malicious logon happen?

<img width="1920" height="1080" alt="9" src="https://github.com/user-attachments/assets/2b463246-4ec5-4af3-a63a-819a8fb31bbc" />


**Answer: 2024-07-31 04:55:16**

Q) What is the share Name accessed as part of the authentication process by the malicious tool used by the attacker?

**Windows Security Log Event ID 5140 a network share object was accessed**

<img width="1920" height="1080" alt="10" src="https://github.com/user-attachments/assets/1c4ffe56-9917-4560-b91f-f68a438fb041" />


**Answer: \\*\IPC$**
