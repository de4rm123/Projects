Scenario:

 One of our WordPress sites has been compromised but we're currently unsure how. The primary hypothesis is that an installed plugin was vulnerable to a remote code execution vulnerability which gave an attacker access to the underlying operating system of the server. 

Artifacts:

        - access.log
IOC:

        - cat access.log | cut -d "\"" -f 6 | sort | uniq
                - WPScan v3.8.10 (https://wpscan.org/)
                - sqlmap/1.4.11#stable (http://sqlmap.org)
                - python-requests/2.24.0
Attacker IP:

        - cat access.log | grep -i python-requests/2.24.0
        
<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/5dfcd975-3a6f-4449-b1eb-0709a20352f5" />


Q) Identify the URI of the admin login panel that the attacker gained access to (include the token)
**cat access.log | grep -i 119.241.22.121**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/e8096f54-e36c-4693-ba81-4d47b64e0fb0" />


**Answer: /wp-login.php?itsec-hb-token=adminlogin**

Q) Can you find two tools the attacker used?

**cat access.log | cut -d "\"" -f 6 | sort | uniq**

**Answer: wpscan,sqlmap

Q) The attacker tried to exploit a vulnerability in ‘Contact Form 7’. What CVE was the plugin vulnerable to? 

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/63dc7940-a670-49f2-893a-199cf70abc46" />


**Answer: CVE-2020-35489**

Q) What plugin was exploited to get access?

**cat access.log | grep -i 119.241.22.121 | grep -i plugins**

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/4dcfa350-39fd-4006-b705-071013a8ab22" />
<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/efb71469-ab06-4d12-9c08-1c98b16f0a28" />


**Answer: Simple File List 4.2.2**

Q) What is the name of the PHP web shell file?

**cat access.log | grep -i 119.241.22.121**

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/96929ac3-318c-40e3-91bc-6d321dce1b0f" />
<img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/6ee6ba95-901d-4a01-9762-ce3d16d9d313" />


**Answer: Fr34k.php**

Q) What was the HTTP response code provided when the web shell was accessed for the final time?

**cat access.log | grep -i Fr34k**

<img width="1920" height="1080" alt="8" src="https://github.com/user-attachments/assets/66b03217-5600-4690-bd3b-742776c62d8c" />


**Answer: 404**
