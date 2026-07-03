Scenario:

 A server with sensitive data was accessed by an attacker and the files were posted on an underground forum. This data was only available to a privileged user, in this case the ‘root’ account. Responders say ‘www-data’ would be the logged in user if the server was remotely accessed, and this user doesn’t have access to the data. The developer stated that the server is hosting a PHP-based website and that proper filtering is in place to prevent php file uploads to gain malicious code execution. The bash history is provided to you but the recorded commands don’t appear to be related to the attack. Can you find what actually happened? 

Artifacts:

        - bash_history

1. Analysis of the bash history file revealed command execution traces from two users: root and daniel.

  <img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/c763423a-750e-48a8-ae4e-1c53b41a93e5" />

2. The attacker attempted to download the script linux-exploit-suggester.sh, which is commonly used for local privilege escalation enumeration. However, no evidence of execution was observed in the available artifacts, suggesting that the script was either not run.

  <img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/74987eed-2f1c-4a28-b675-bde81338b164" />

3. The attacker attempted to use tcpdump, a command-line packet analysis tool commonly used for network traffic inspection and credential or data capture.

  <img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/1fe1fec8-6220-4460-887f-0d0775c20f50" />

4. Evidence indicates that the attacker removed the file x.phtml, which was previously used to bypass the file upload filter.

  <img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/fa8bae35-56f7-47e5-9da1-25adf89bc867" />

5. Evidence indicates that the attacker discovered a Python binary with the SUID bit set, which allowed execution with elevated privileges.

  <img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/026530c9-7886-40cf-9800-6377ca81bef9" />

Conclusion:

The investigation reveals a full Linux system compromise driven by multiple stages of attacker activity, starting from initial web-based access through a file upload bypass using the malicious x.phtml file. Evidence shows that the attacker later removed this file, indicating post-exploitation cleanup and anti-forensic behavior.
The attacker attempted to download linux-exploit-suggester.sh, suggesting efforts to identify local privilege escalation vectors; however, no evidence of execution was found, indicating the script was either not run or execution traces were removed.
Additional activity shows attempted use of tcpdump, indicating potential network reconnaissance or traffic interception efforts, though no supporting artifacts confirm successful packet capture. The investigation also identified a critical privilege escalation path involving a Python binary with the SUID bit set, which could allow execution of commands with elevated privileges, likely enabling root-level compromise.
