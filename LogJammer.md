Scenario:

You have been presented with the opportunity to work as a junior DFIR consultant for a big consultancy. However, they have provided a technical assessment for you to complete. The consultancy Forela-Security would like to gauge your Windows Event Log Analysis knowledge. We believe the Cyberjunkie user logged in to his computer and may have taken malicious actions. Please analyze the given event logs and report back.

Artifacts:

        - Powershell-Operational.evtx
        - Security.evtx
        - System.evtx
        - 'Windows Defender-Operational.evtx'
        - 'Windows Firewall-Firewall.evtx'

Preparations:

        - chainsaw dump Powershell-Operational.evtx --json > powershell-operational.json
        - chainsaw dump Security.evtx --json > security.json
        - chainsaw dump System.evtx --json > system.json
        - chainsaw dump 'Windows Defender-Operational.evtx' --json > windowsDefenderOperational.json
        - chainsaw dump 'Windows Firewall-Firewall.evtx' --json > windowsFirewall.json

Q) When did the cyberjunkie user first successfully log into his computer? (UTC)

**jq '.[] | select(.Event.System.EventID == 4624 and (.Event.EventData.TargetUserName | contains("CyberJunkie")))' Security.json**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/459b89b6-2a41-428b-84cc-0f19da551e62" />


**Answer: 27/03/2023 14:37:09**

Q) The user tampered with firewall settings on the system. Analyze the firewall event logs to find out the Name of the firewall rule added?

**jq '.[] | select(.Event.System.EventID==2004)' WindowsFirewall.json**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/39eb0c9e-4f0d-4ceb-95e9-30b4a8add974" />


**Answer: Metasploit C2 Bypass**

Q) Whats the direction of the firewall rule?

**jq '.[] | select(.Event.System.EventID==2004)' WindowsFirewall.json**

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/3887605e-50b0-4663-a6f9-241f84c210cd" />


In Windows Firewall logs, Direction 2 represents Outbound traffic.

**Answer: Outbound**

Q) The user changed audit policy of the computer. Whats the Subcategory of this changed policy?

**jq '.[] | select(.Event.System.EventID==4719)' Security.json**

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/e2643ab1-6e0d-432e-83c7-9ea2750eef56" />


**Answer: Other Object Access Events**

Q) The user "cyberjunkie" created a scheduled task. Whats the name of this task?

**jq '.[] | select(.Event.System.EventID == 4698)' Security.json**

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/de74e720-9bd6-4573-bc79-b4d47eb608b5" />


**Answer: HTB-AUTOMATION**

Q) Whats the full path of the file which was scheduled for the task?

**jq '.[] | select(.Event.System.EventID == 4698)' Security.json**

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/16fe6991-34b9-4738-818c-7494a3a3e4e5" />


**Answer: C:\Users\CyberJunkie\Desktop\Automation-HTB.ps1**

Q) What are the arguments of the command?

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/d3b1c005-b438-464c-9552-832e853dafd5" />


**Answer: -A cyberjunkie@hackthebox.eu**

Q) The antivirus running on the system identified a threat and performed actions on it. Which tool was identified as malware by antivirus?

**jq '.[] | select(.Event.System.EventID==1116)' WindowsDefenderOperational.json**

<img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/5cdc7f3c-b981-40b5-a22f-75f8b37a8029" />


**Answer: Sharphound**

Q) Whats the full path of the malware which raised the alert?

**jq '.[] | select(.Event.System.EventID==1116)' WindowsDefenderOperational.json**

<img width="1920" height="1080" alt="8" src="https://github.com/user-attachments/assets/387cfe7f-9247-4c27-907c-563f6924f714" />


**Answer: C:\Users\CyberJunkie\Downloads\SharpHound-v1.1.0.zip**

Q) What action was taken by the antivirus?

**jq '.[] | select(.Event.System.EventID==1117)' WindowsDefenderOperational.json**

<img width="1920" height="1080" alt="9" src="https://github.com/user-attachments/assets/d66f81d2-66bd-44ca-a435-4b1c83c6bd92" />


**Answer: Quarantine**

Q) The user used Powershell to execute commands. What command was executed by the user?

**jq '.[] | select(.Event.System.EventID==4104)' Powershell-Operational.json**

<img width="1920" height="1080" alt="10" src="https://github.com/user-attachments/assets/9d3a63d8-9847-4841-a026-d23d8f66badf" />


**Answer: Get-FileHash -Algorithm md5 .\Desktop\Automation-HTB.ps1**

Q) We suspect the user deleted some event logs. Which Event log file was cleared?

**jq '.[] | select(.Event.System.EventID == 1102)' Security.json**

<img width="1920" height="1080" alt="11" src="https://github.com/user-attachments/assets/3de56d30-1e8c-484f-a318-07d7c8604c88" />


**Answer: Microsoft-Windows-Windows Firewall With Advanced Security/Firewall**
