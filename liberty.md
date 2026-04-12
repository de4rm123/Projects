Scenario:

Liberty launched a massive project with a budget exceeding $100 million, with the goal of granting true freedom to humanity.
One day, a system administrator discovered a shared folder configured with “Full Control” permissions granted to “Everyone”, raising concerns about a potential security incident.
You have been tasked with investigating evidence collected from the affected endpoint to determine what occurred. Threat Intelligence has previously identified that an employee’s credentials were harvested by a RedLine Stealer, which is suspected to have been used for initial access to this system.

Artifacts:

        - $MFT
        - $J
        - Inetpub logs
        - winevt logs
        - DEFAULT, SAM, SECURITY, SOFTWARE, SYSTEM hives
        - C:\Windows\System32\Tasks
        - Users directory

Preparations:

        - chainsaw dump \$MFT --json > mft.json
        - chainsaw dump security.evtx --json > security.json
        - chainsaw dump Microsoft-Windows-PowerShellWebAccess\ Operational.evtx --json > windows-powershellwebaccess.json
        - hivexsh(interactive shell in windows hives)

Q) You suspect that a threat actor might conduct password spraying attack on this server, How many failed logon attempts identified before successfully identifying the correct pair of the credential?

**jq '[.[] | select(.Event.System.EventID==4625 and (.Event.EventData.IpAddress | contains("192.168.189.129")) and .Event.EventData.TargetUserName != "user")] | length' security.json**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/3be336a7-7a6a-48fa-aa0a-7b74e718804a" />
<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/b97f1624-b447-493a-90c1-f0ab6e48b943" />


**Answer: 5**

Q) What is the user that was identified by the threat actor?

**jq '.[] | select(.Event.System.EventID==4626 and (.Event.EventData.IpAddress | contains("192.168.189.129")))' security.json**

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/c1d1c74f-3a74-46b7-822b-8e510b1ef7d3" />


**Answer: v.hunter**

Q) There is a shared folder that can be accessed by all users, what is the name of this shared folder?

**hivexget SYSTEM 'ControlSet001\Services\LanmanServer\Shares' Proposal**

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/909162f7-15bd-4136-9a51-0bbb2578eb49" />


**Proposal**

Q) The threat actor uploaded several files to the previously identified shared folder. One of these files can be used to capture the hash of a user who opens it. What is the name of that file?

**jq '.[] | select(.FullPath | contains("Proposal"))' mft.json**

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/1a43078f-41b0-47c9-87a5-c591e370a13a" />


**Answer: Proposal.url**


Q) What is the full URL used by threat actor to mimic the fake proposal of the project?

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/0725fa41-8ef1-4e6a-bd0b-d951e59dc841" />


**Answer: http://argonaut.ark/proposal.html**

Q) What is the full UNC path of the network share that the threat actor used to capture hash of the victim?

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/2aaf2388-278e-4527-a2a1-4b0d0a16fedb" />


**Answer: \\192.168.189.129\%USERNAME%.icon**

Q) What is the full name of the second compromised user?

**hivexget SAM "SAM\Domains\Account\Users\000003EA"**
**hivexget SAM "SAM\Domains\Account\Users\000003EA" V**

<img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/2dcdd417-68af-475a-8f48-1b6d3fd215ba" />


**Answer: Kuneo Texus**

Q) When was the time that the threat actor connected to the server via RDP in UTC?

**jq '.[] | select(.Event.System.EventID==4624 and .Event.EventData.LogonType==10)' security.json**

<img width="1920" height="1080" alt="8" src="https://github.com/user-attachments/assets/3a9d8789-db7a-49d2-b75e-39b55a765dd5" />


**Answer: 2025-06-11 14:44:48**

Q) The threat actor discovered a folder that stores files about the project, What is the full path of this folder?

**hivexget SYSTEM 'ControlSet001\Services\LanmanServer\Shares'**
**hivexget SYSTEM 'ControlSet001\Services\LanmanServer\Shares' ProjectArk**

<img width="1920" height="1080" alt="9" src="https://github.com/user-attachments/assets/cf8748c1-4ab4-465e-8c12-84f6a2929717" />


**Answer: C:\ProjectArk**

Q) The threat actor created an archive file containing all files of the previously identified folder, What is the name of this archive file?

C:\Users\k.texus\Appdata\Roaming\Microsoft\Windows\Recent

   ├── arkproj.zip.lnk

**jq '.[] | select(.FullPath | contains("arkproj.zip"))' mft.json**

<img width="1920" height="1080" alt="10" src="https://github.com/user-attachments/assets/f376cfee-c684-4573-85c5-17aa2c180088" />


**Answer: arkproj.zip**

Q) What is the total bytes of all files on that folder which were compressed into previously identified archive file? (not including Zone Identifier)

**jq '.[] | select(.FullPath | contains("ProjectArk"))' mft.json**

**Answer: 783907**

Q) While reviewing users on this server, you found a suspicious user on this server, What is the name of this user?

**C:\Users\k.texus\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt**

<img width="1920" height="1080" alt="11" src="https://github.com/user-attachments/assets/ba211d52-8f5f-4d54-9564-296be37978f9" />


**Answer: t.minami**

Q) The threat actor installed a web-based gateway as a backdoor to the server. What is the full command used to install this feature?

**C:\Users\k.texus\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt**

<img width="1920" height="1080" alt="11" src="https://github.com/user-attachments/assets/2c2bfc56-4a22-469b-9422-469024d40648" />


**Answer: Install-WindowsFeature -Name WindowsPowerShellWebAccess -IncludeManagementTools**

Q) Which protocol has to be enabled to use this feature?

**C:\Users\k.texus\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt**

<img width="1920" height="1080" alt="11" src="https://github.com/user-attachments/assets/d4a146db-0465-4396-9c22-ce09f9f75ac7" />


**Answer: WinRM**

Q) Provide the UTC timestamp when the threat actor confirmed successful backdoor access through the previously identified user account.

**jq '.[] | select(.Event.EventData.OriginIpAddressRemoteAddr=="192.168.189.129")' powershelloperationallWebaccess.json**

<img width="1920" height="1080" alt="12" src="https://github.com/user-attachments/assets/1bee564a-2ce8-4b6d-9e8c-e5d6408c73ad" />


**Answer: 2025-06-11 14:54:55**

Q) What is the Session ID of this connection?

**jq '.[] | select(.Event.EventData.OriginIpAddressRemoteAddr=="192.168.189.129")' powershelloperationallWebaccess.json**

<img width="1920" height="1080" alt="12" src="https://github.com/user-attachments/assets/df16a2b7-3efe-44e7-8337-8fefd8582ec7" />


**Answer: LIBERYSV08\t.minami.250611.075455**

Q) Provide the UTC timestamp When was this session terminated by the threat actor

**jq '.[] | select(.Event.EventData.EndType)' powershelloperationallWebaccess.json**

<img width="1920" height="1080" alt="13" src="https://github.com/user-attachments/assets/641406d6-e627-4d6b-bf2c-7db451bf5582" />


**Answer: 2025-06-11 14:55:40**

Q) What is the name of shared folder that was created by the threat actor during the invasion?

**cat /$Recycle.Bin/S-1-5-21-3473629566-4134604280-926640063-1002/$ILDSQT3.zip**

**Answer: ProjectArk**
