Scenario:

Forela's CTO, Dutch, stores important files on a separate Windows system because the domain environment at Forela is frequently breached due to its exposure across various industries. On 24 January 2025, our worst fears were realised when an intruder accessed the fileserver, installed utilities to aid their actions, stole critical files, and then deleted them, rendering them unrecoverable. The team was immediately informed of the extortion attempt by the intruders, who are now demanding money. While our legal team addresses the situation, we must quickly perform triage to assess the incident's extent. Note from the manager: We enabled SmartScreen Debug Logs across all our machines for enhanced visibility a few days ago, following a security research recommendation. These logs can provide quick insights, so ensure they are utilised.

Artifacts:

        - Windows Events

Preparations:

        - chainsaw dump Security.evtx --json > security.json
        - chainsaw dump Microsoft-Windows-TerminalServices-RemoteConnectionManager%4Operational.evtx --json > RemoteConnectionManager.json
        - chainsaw dump  Microsoft-Windows-SmartScreen%Debug.evtx --json > SmartScreen.json

Q) The attacker logged in to the machine where Dutch saves critical files, via RDP on 24th January 2025. Please determine the timestamp of this login.

**1149 (TerminalServices-RemoteConnectionManager)**: Indicates a user has successfully connected at the network level and received a login screen.

**jq '.[] | select(.Event.System.EventID == 1149)' RemoteConnectionManager.json**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/7c92e221-e7f5-4a56-82a2-1d173d19de0c" />


**Answer: 2025-01-24 10:15:14**

Q) The attacker downloaded a few utilities that aided them for their sabotage and extortion operation. What was the first tool they downloaded and installed?

**jq '.[] | select(.Event.EventData.Data | contains("Downloads"))' SmartScreen.json**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/b973f0c8-85d4-4290-ba41-c91e46088576" />


**Answer: WinRAR**

Q) They then proceeded to download and then execute the portable version of a tool that could be used to search for files on the machine quickly and efficiently. What was the full path of the executable?

**jq '.[] | select(.Event.EventData.Data | contains("Downloads"))' SmartScreen.json**

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/d36d0d58-bf2c-4a7d-b0c6-57ef1292d9a7" />


**Answer: C:\Users\Dutch\Downloads\Everything.exe**

Q) What is the execution time of the tool from task 3?

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/40d64fa8-de55-46c0-87a0-25c49b584696" />


**Answer: 2025-01-24 10:17:33**

Q) The utility was used to search for critical and confidential documents stored on the host, which the attacker could steal and extort the victim. What was the first document that the attacker got their hands on and breached the confidentiality of that document?

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/21bd656b-75e0-4464-b82d-52fb143efde4" />


**Answer: C:\Users\Dutch\Documents\2025- Board of directors Documents\Ministry Of Defense Audit.pdf**

Q) Find the name and path of second stolen document as well.

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/89ba95b6-a0a5-452d-be49-d764970ee758" />


**C:\Users\Dutch\Documents\2025- Board of directors Documents\2025-BUDGET-ALLOCATION-CONFIDENTIAL.pdf**

Q) The attacker installed a Cloud utility as well to steal and exfiltrate the documents. What is name of the cloud utility?

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/98b536b5-45f5-4c11-9cec-497187ff0735" />
<img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/c804febb-3147-4c1c-aa1b-d121dec12b24" />


**Answer: MEGAsync**

Q) When was this utility executed?

<img width="1920" height="1080" alt="8" src="https://github.com/user-attachments/assets/7465571c-4c23-4a64-8d83-43d2bd32b38a" />


**Answer: 2025-01-24 10:22:19**

Q) The Attacker also proceeded to destroy the data on the host so it is unrecoverable. What utility was used to achieve this?

<img width="1920" height="1080" alt="9" src="https://github.com/user-attachments/assets/ef3cab45-a565-40c1-9183-d0d9476424e0" />


**Answer: File Shredder**

Q) The attacker cleared 2 important logs, thinking they covered all their tracks. When was the security log cleared?

**jq '.[] | select(.Event.System.EventID == 1102)' Security.json**

<img width="1920" height="1080" alt="10" src="https://github.com/user-attachments/assets/1059745c-b49e-4c48-aa21-7e134af72696" />


**Answer: 2025-01-24 10:28:41**
