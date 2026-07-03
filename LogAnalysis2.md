Scenario:

 One of our WordPress sites has been compromised but we're currently unsure how. The primary hypothesis is that an installed plugin was vulnerable to a remote code execution vulnerability which gave an attacker access to the underlying operating system of the server. 

Artifacts:

        - access.log
IOC:

        - cat access.log | cut -d "\"" -f 6 | sort | uniq
                - WPScan v3.8.10 (https://wpscan.org/)
                - sqlmap/1.4.11#stable (http://sqlmap.org)
                - python-requests/2.24.0
                
1. To identify the source IP address of the attacker, web server access logs should be analyzed. While the User-Agent field helps identify automated tools or malicious clients.

  **cat access.log | grep -i python-requests/2.24.0**
        
  <img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/5dfcd975-3a6f-4449-b1eb-0709a20352f5" />

2. With the identified attacker IP address and corresponding User-Agent, web server logs can be further analyzed to extract precise timestamps of malicious activity. By correlating requests from this IP and User-Agent combination, it is possible to reconstruct the attacker’s actions on the server, including the exact time of initial access, exploited endpoints, and the type of access gained.
 
  **cat access.log | grep -i 119.241.22.121**

  <img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/e8096f54-e36c-4693-ba81-4d47b64e0fb0" />

3. Analysis of web server logs and request metadata indicates that the attacker utilized automated security testing tools, specifically WPScan and sqlmap, during the compromise.

4. The attacker targeted Contact Form 7 version 5.3.1, indicating a deliberate attempt to exploit a known vulnerability in a widely used WordPress plugin.

  <img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/63dc7940-a670-49f2-893a-199cf70abc46" />

5. Log file indicates that the attacker abused the WordPress plugin “Simple File List” version 4.2.2 to upload a malicious web shell.

  **cat access.log | grep -i 119.241.22.121 | grep -i plugins**

  <img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/4dcfa350-39fd-4006-b705-071013a8ab22" />
  <img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/efb71469-ab06-4d12-9c08-1c98b16f0a28" />

6. Attacker upload a file with name fr34k.png

  **cat access.log | grep -i 119.241.22.121**

  <img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/96929ac3-318c-40e3-91bc-6d321dce1b0f" />
  <img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/6ee6ba95-901d-4a01-9762-ce3d16d9d313" />

7. Searching for the keyword “fr34k” across available logs reveals activity associated with a second IP address, 103.69.55.212 and a fr34k.php file.

  **cat access.log | grep -i Fr34k**

  <img width="1920" height="1080" alt="8" src="https://github.com/user-attachments/assets/66b03217-5600-4690-bd3b-742776c62d8c" />

Conclusion:

The investigation confirms that the WordPress site was successfully compromised through exploitation of vulnerable plugins. Initial reconnaissance activity indicates the use of automated tools such as WPScan and SQLMap, which were used to enumerate the WordPress environment and identify potential attack surfaces.
The primary initial access vector was identified as the exploitation of the Simple File List plugin (version 4.2.2), which allowed the attacker to upload a malicious file masquerading as an image, fr34k.png. This file is strongly suspected to contain or facilitate web shell functionality, enabling remote command execution on the underlying server.
Subsequent log analysis revealed additional suspicious activity associated with the keyword “fr34k”, which correlated with a secondary IP address 103.69.55.212. This suggests potential interaction with attacker-controlled infrastructure, possibly used for command-and-control communication or post-exploitation operations.
