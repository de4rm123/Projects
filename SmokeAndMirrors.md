Scenario:

Byte Doctor Reyes is investigating a stealthy post-breach attack where several expected security logs and Windows Defender alerts appear to be missing. He suspects the attacker employed defense evasion techniques to disable or manipulate security controls, significantly complicating detection efforts.

Using the exported event logs, your objective is to uncover how the attacker compromised the system's defenses to remain undetected.

Artifacts:

        - Microsoft-Windows-Powershell.evtx
        - Microsoft-Windows-Powershell-Operational.evtx
        - Microsoft-Windows-Sysmon-Operational.evtx

Preparations:

        - chainsaw dump Microsoft-Windows-Sysmon-Operational.evtx --json > Sysmon.json
        - chainsaw dump Microsoft-Windows-Powershell.evtx --json > powershell.json
        - chainsaw dump Microsoft-Windows-Powershell-Operational.evtx --json > PowershellOperational.json

Q) The attacker disabled LSA protection on the compromised host by modifying a registry key. What is the full path of that registry key?

**jq '.[] | select(.Event.System.EventID == 4104 and (.Event.EventData.ScriptBlockText | contains("reg")))' PowershellOperational.json**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/139e435d-969a-4dd8-930a-759720052bac" />


**Answer: HKLM\SYSTEM\CurrentControlSet\Control\LSA**

Q) Which PowerShell command did the attacker first execute to disable Windows Defender?

**jq '.[] | select(.Event.System.EventID == 4104 and (.Event.EventData.ScriptBlockText | contains("Disable")))' PowershellOperational.json**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/1a092814-155a-4ef7-b86b-a0060e6ac13d" />


**Answer: Set-MpPreference -DisableIOAVProtection $true -DisableEmailScanning $true -DisableBlockAtFirstSeen $true**

Q) The attacker loaded an AMSI patch written in PowerShell. Which function in the DLL is being patched by the script to effectively disable AMSI?

**jq '.[] | select(.Event.System.EventID == 4104 and (.Event.EventData.ScriptBlockText | contains("Disable")))' PowershellOperational.json**

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/06fd21c1-ba7d-4788-954d-8100784152ea" />


**Answer: AmsiScanBuffer**

Q) Which command did the attacker use to restart the machine in Safe Mode?

**jq '.[] | select(.Event.System.EventID == 1)' Sysmon.json**

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/84842d21-8582-4d70-8096-c371a599cf8a" />


**Answer: bcdedit.exe /set safeboot network**

Q) Which PowerShell command did the attacker use to disable PowerShell command history logging?

**jq '.[] | select(.Event.System.EventID == 4104 and (.Event.EventData.ScriptBlockText | test("History")))' PowershellOperational.json**

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/4e174cb5-d0e8-49d0-bc07-6754aaf3cd8e" />


**Answer: Set-PSReadlineOption -HistorySaveStyle SaveNothing**
