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

Steps:

1. After sorted the timestamp from the oldest to newest, found a logon attempt with explicit credentials and cyberjunkie as it's username. This is the first attempt to logon with username **CyberJunkie**.

   **jq '.[] | select(.Event.System.EventID == 4648 and (.Event.EventData.TargetUserName | contains("CyberJunkie")))' Security.json**


   <img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/7f68da69-f2a6-49e3-8061-ca000f868a0c" />


2. Analyzing the Windows Firewall let us identify the name of the firewall rule added.

   <img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/39eb0c9e-4f0d-4ceb-95e9-30b4a8add974" />

3. The direction of the firewall rule is also stated there.In Windows Firewall logs, Direction 2 represents Outbound traffic.
   
   **jq '.[] | select(.Event.System.EventID==2004 and (.Event.EventData.Direction))' WindowsFirewall.json**

   <img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/3887605e-50b0-4663-a6f9-241f84c210cd" />

4. To verify whether the audit policy was changed, check for Event ID 4719 in the Windows Security Event Log. This event is generated whenever the system's audit policy is modified, making it a key indicator for detecting changes to auditing configurations.

   **jq '.[] | select(.Event.System.EventID==4719)' Security.json**

   <img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/e2643ab1-6e0d-432e-83c7-9ea2750eef56" />

5. To determine whether a new scheduled task was created, review Event ID 4698 in the Windows Security Event Log. This event is generated whenever a scheduled task is created on the system. It records valuable information such as the task name, the account that created it and any configured actions or triggers.
   
   **jq '.[] | select(.Event.System.EventID == 4698)' Security.json**

   <img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/de74e720-9bd6-4573-bc79-b4d47eb608b5" />

6. Within the TaskContent field, the full path to the script or executable can be found and the arguments.

   **jq '.[] | select(.Event.System.EventID == 4698)' Security.json**

   <img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/16fe6991-34b9-4738-818c-7494a3a3e4e5" />

7. Event ID 1116 indicates that Microsoft Defender Antivirus detected a threat. This event includes important details such as the threat name, severity level, detection time, the affected file or process, and the action taken by Microsoft Defender.

   **jq '.[] | select(.Event.System.EventID==1116)' WindowsDefenderOperational.json**

   <img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/5cdc7f3c-b981-40b5-a22f-75f8b37a8029" />
   <img width="1920" height="1080" alt="8" src="https://github.com/user-attachments/assets/387cfe7f-9247-4c27-907c-563f6924f714" />

8. Event ID 1117 records the action taken by Microsoft Defender Antivirus against a detected threat, allowing us to determine whether the threat was quarantined, removed, cleaned, or otherwise handled.

   jq '.[] | select(.Event.System.EventID == 1117 and .Event.EventData["Action\\Name"])' WindowsDefenderOperational.json

   <img width="1920" height="1080" alt="9" src="https://github.com/user-attachments/assets/d66f81d2-66bd-44ca-a435-4b1c83c6bd92" />

9. While analyzing the Security event log, I noticed there is an event log which is cleared by cyberjunkie.

   **jq '.[] | select(.Event.System.EventID == 1102)' Security.json**

   <img width="1920" height="1080" alt="11" src="https://github.com/user-attachments/assets/3de56d30-1e8c-484f-a318-07d7c8604c88" />

Conclusion:

   The evidence indicates that the attacker attempted to establish persistence by creating a scheduled task, modified the Windows Firewall to permit unauthorized network communication,    executed a PowerShell script to automate malicious actions, and later cleared the firewall event log to hinder forensic analysis.
