Scenario:

Talion suspects that the threat actor carried out anti-virtualization checks to avoid detection in sandboxed environments. Your task is to analyze the event logs and identify the specific techniques used for virtualization detection. Byte Doctor requires evidence of the registry checks or processes the attacker executed to perform these checks.

Artifacts:

        - Microsoft-Windows-Powershell.evtx
        - Windows-Powershell-Operational.evtx

Preparations:

        - chainsaw dump Microsoft-Windows-Powershell.evtx --json > MicrosoftWindowsPowershell.json
        - chainsaw dump Windows-Powershell-Operational.evtx --json > WindowsPowershellOperational.json

Q) Which WMI class did the attacker use to retrieve model and manufacturer information for virtualization detection?

**jq '.[] | select(.Event.EventData.Data["#text"][] | contains("Win32"))' MicrosoftWindowsPowershell.json**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/24ba2957-836b-46ab-88c7-c424907310c3" />


**Answer: Win32_ComputerSystem**

Q) Which WMI query did the attacker execute to retrieve the current temperature value of the machine?

**jq '.[] | select(.Event.EventData.Data["#text"][] | contains("Temp"))' MicrosoftWindowsPowershell.json**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/2d772e1d-6d8a-4dd5-b173-231ee3fc85ff" />


**Answer: SELECT * FROM MSAcpi_ThermalZoneTemperature**

Q) The attacker loaded a PowerShell script to detect virtualization. What is the function name of the script?

Event ID 4105 represents the start of a PowerShell script block execution.

**jq '.[] | select(.Event.System.EventID == 4104 and (.Event.EventData.ScriptBlockText | contains("function")))' WindowsPowershellOperational.json**

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/907557b2-8259-4f1f-bde2-5cf2b0873c3c" />


**Answer: Check-VM**

Q) Which registry key did the above script query to retrieve service details for virtualization detection?

**jq '.[] | select(.Event.System.EventID == 4104 and (.Event.EventData.ScriptBlockText | contains("function")))' WindowsPowershellOperational.json**

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/b1535fc0-c3e7-41ec-aa6a-5b35759fa031" />


**Answer: HKLM:\SYSTEM\ControlSet001\Services**

Q) The VM detection script can also identify VirtualBox. Which processes is it comparing to determine if the system is running VirtualBox?

**jq '.[] | select(.Event.System.EventID == 4104 and (.Event.EventData.ScriptBlockText | contains("function")))' WindowsPowershellOperational.json**

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/2bdc9e31-f972-4813-94fa-7ea1d1b8e7aa" />


**Answer: vboxservice.exe, vboxtray.exe**

Q) The VM detection script prints any detection with the prefix 'This is a'. Which two virtualization platforms did the script detect?

**jq '.[] | select(.Event.System.EventID == 4104 and (.Event.EventData.ScriptBlockText | contains("function")))' WindowsPowershellOperational.json | grep -i "This is a"**

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/7aab6d46-6386-409e-a16a-1fc7857b9345" />


**Answer: Hyper-V, Vmware**
