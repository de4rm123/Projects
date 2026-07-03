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


1. After identifying the user accounts present on the system, the auth.log file was analyzed to review authentication activity. The investigation revealed multiple failed login attempts, indicating that an account was targeted by repeated authentication failures.These events may represent a brute-force attack and should be correlated with subsequent successful logins, source IP addresses, and timestamps to determine whether the attacker eventually gained unauthorized access.

**cat auth.log | grep -i failed | less**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/88cbec5c-ac3a-4ea8-abfa-d3371bab1155" />

2. Analysis of the authentication logs identified a successful login for the administrator account originating from the internal IP address 192.168.161.198. This successful authentication followed a series of failed login attempts, indicating that the attacker likely obtained unauthorized access using the administrator account.

**cat auth.log | grep -i accepted | grep -i administrator**

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/b8a4de00-2286-4e05-89a1-3e47daf2b296" />

3. Following the successful initial access, the attacker began performing system enumeration, as evidenced by commands recorded in the .bash_history file. One of the observed commands was groups administrator, which was used to identify the group memberships and privileges assigned to the administrator account.

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/a49a4dd2-0528-4bae-963a-702ee1535237" />

4. Further analysis of the .bash_history file indicates that the attacker downloaded LaZagne, an open-source credential recovery tool used to extract stored passwords.

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/020475bf-2860-4797-a1d3-6f23229603bd" />

5. Analysis of the command history revealed that the attacker used the scp command-line utility to transfer sensitive files from the compromised system to a remote host.

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/7ddd76b9-0e95-4efa-b385-8e0634dfdd3d" />

6. The attacker continued their exploitation and executed a malicious script sys_monitor.sh on the victim machine.

<img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/8b125a67-3bff-414f-a911-64c618006550" />

7. Analysis of the system configuration revealed that the attacker established persistence by creating a malicious systemd service named systemd-networkm.service.

<img width="1920" height="1080" alt="9" src="https://github.com/user-attachments/assets/e6354c92-d1b2-4bf8-ae40-47329a1db859" />

8. Further analysis revealed that the attacker modified several user startup configuration files, including .bashrc, to establish persistence. Malicious commands were added to these files to automatically start network listeners on different ports whenever a user logged in.

**cat root/.bashrc**

<img width="1920" height="1080" alt="10" src="https://github.com/user-attachments/assets/46915c69-f6c9-403d-9399-c390aff4d559" />

9. Analysis of the auth.log revealed that the attacker created a new user account named Regev on 2025-02-10 20:11:21 to establish persistence on the compromised host. 

**cat auth.log | grep -i add**

<img width="1920" height="1080" alt="12" src="https://github.com/user-attachments/assets/17c7a222-15cb-4a42-b300-8c9487dcf62e" />

<img width="1920" height="1080" alt="13" src="https://github.com/user-attachments/assets/f2d0c973-7cf7-4f74-a069-a195e34634f8" />

10. The malware set up an automated process to fetch and execute a remote payload from a legitimate web service.


<img width="1920" height="1080" alt="14" src="https://github.com/user-attachments/assets/ef93adfd-dcf9-4299-9605-c448dadfad5a" />

**cat /etc/cron.d/syscheck**

<img width="1920" height="1080" alt="15" src="https://github.com/user-attachments/assets/50e3a6a1-bbb5-4e99-9a2f-aba7b4d6fa14" />


11. The payload was used to extract more sensitive files.
    
<img width="1920" height="1080" alt="16" src="https://github.com/user-attachments/assets/377712de-dd41-492f-891e-6b0d3ee85c24" />

<img width="1920" height="1080" alt="17" src="https://github.com/user-attachments/assets/c7035a14-eb2a-4b52-a60a-fee3d8d63461" />

Conclusion:

The forensic investigation confirms that the IT Manager's workstation was successfully compromised following unauthorized access from the internal IP address 192.168.161.198 using the administrator account. The internal source of the login suggests the attacker had already compromised another system within the organization's network and used it to pivot to the victim's machine.
After obtaining access, the attacker performed extensive system enumeration, as evidenced by commands recorded in the .bash_history file, including checks of user group memberships and system privileges. The attacker then downloaded LaZagne, a credential harvesting tool, indicating an attempt to recover stored credentials for privilege expansion and potential lateral movement.
The investigation also revealed that the attacker collected and exfiltrated sensitive files, including Passwords_backup.txt and Server_credential.txt, using the scp utility to securely transfer the data to a remote system. This confirms that sensitive organizational information was accessed and removed from the compromised host.
Following the data theft, the attacker executed a malicious script named sys_monitor.sh, indicating continued post-exploitation activity. To maintain long-term access, the attacker established multiple persistence mechanisms, including the creation of a malicious systemd service (systemd-networkm.service), modifications to shell startup files such as .bashrc to automatically launch network listeners at user login, and the creation of a new user account named Regev on 2025-02-10 20:11:21.
