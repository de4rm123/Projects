Scenario:

You were contacted early this morning to handle a high‑priority incident involving a suspected compromised server. The host, mongodbsync, is a secondary MongoDB server. According to the administrator, it's maintained once a month, and they recently became aware of a vulnerability referred to as MongoBleed. As a precaution, the administrator has provided you with root-level access to facilitate your investigation.
You have already collected a triage acquisition from the server using UAC. Perform a rapid triage analysis of the collected artifacts to determine whether the system has been compromised, identify any attacker activity (initial access, persistence, privilege escalation, lateral movement, or data access/exfiltration), and summarize your findings with an initial incident assessment and recommended next steps.

Artifacts:

        - .bash_history
        - auth.log
        - mongod.log

1. The MongoDB version should be identified primarily from the mongod.log file, where the service typically logs its startup banner, including version information and build details.This version information is critical for determining whether the system is vulnerable to known exploits such as those associated with “MongoBleed.” Once the version is confirmed, it should be cross-referenced with known CVEs to assess potential exposure and exploitability.

**cat auth.log | grep -i mongodb**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/1b914097-6cbf-4a25-8a50-e78ea7b7216f" />

**cat mongod.log | grep -i version**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/73689202-1165-45e3-854b-5f2d99ef0dba" />


<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/f717c118-75df-47ee-ac38-5cca04558192" />

2. The identified MongoDB version is is confirmed to be vulnerable to CVE-2025-14847. This vulnerability allows unauthenticated remote clients to access uninitialized heap memory through malformed compressed protocol messages, potentially exposing sensitive in-memory data such as credentials, tokens, and configuration details.

**cat mongod.log | grep -i version**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/8a3db03d-140e-4a04-873e-33c4f4305da4" />

3. The mongod.log file can be analyzed to identify connection lifecycle events, including when client connections are established and terminated.

**cat mongod.log | grep -i ended | less**

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/71af0bec-634d-4026-8fa9-b7148652dc3f" />

4. Analysis of the mongod.log file reveals multiple “connection ended” events associated with the external IP address 65.0.76.43. The repeated presence of this IP in connection lifecycle logs indicates active interaction with the MongoDB service start at 2025-12-29 05:25:52.

**cat mongod.log | grep -i ended | less**

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/2c64c5b4-8016-4623-b7b2-f3423efaa877" />

5. Analysis of the authentication logs indicates that the attacker successfully gained remote access to the server after a series of SSH brute-force attempts targeting the mongoadmin account. Following multiple failed authentication attempts, a successful SSH session was established on 2025-12-29 05:40:03 from the external IP address 65.0.76.43.

**cat auth.log | grep -i mongo**

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/5a66d48d-b447-4b82-b6d5-de51d6bf6e58" />

6. Following the successful SSH login, the attacker executed LinPEAS, a Linux privilege escalation enumeration tool commonly used during post-exploitation.

**cat mangoadmin/.bash_history**

<img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/0b39c1c5-47f7-4dea-b641-f3c7988b8c72" />

7. Following system reconnaissance, the attacker used the zip utility to compress a directory named mongodb, likely to prepare its contents for transfer. Shortly afterward, the attacker started a Python HTTP server, which is commonly used to facilitate file transfer during post-exploitation.

**cat mangoadmin/.bash_history**

<img width="1920" height="1080" alt="8" src="https://github.com/user-attachments/assets/b2f2bf74-e364-429d-9693-066eaacd942e" />

Conclusion:

The triage investigation confirms that the mongodbsync server was compromised through a successful SSH brute-force attack against the mongoadmin account. Authentication logs show that, after multiple failed login attempts, the attacker established an SSH session from the external IP address 65.0.76.43 on 2025-12-29 05:40:03, providing interactive access to the system.
Analysis also identified that the server was running MongoDB 8.0.16, a version affected by CVE-2025-14847 (MongoBleed). While this vulnerability represents a significant security risk, the available evidence confirms that the attacker's initial access was achieved through a successful SSH login rather than proving exploitation of the MongoDB vulnerability.
After gaining access, the attacker executed LinPEAS, indicating active post-exploitation reconnaissance to identify privilege escalation opportunities and gather detailed information about the system. The attacker then compressed the mongodb directory using the zip utility, demonstrating data staging activity. Finally, a Python HTTP server was started, strongly suggesting an attempt to transfer the archived data from the compromised host. This behavior is consistent with preparation for data exfiltration and should be correlated with network logs to determine whether the archive was successfully downloaded.
