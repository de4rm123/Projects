Scenario:

 A server with sensitive data was accessed by an attacker and the files were posted on an underground forum. This data was only available to a privileged user, in this case the ‘root’ account. Responders say ‘www-data’ would be the logged in user if the server was remotely accessed, and this user doesn’t have access to the data. The developer stated that the server is hosting a PHP-based website and that proper filtering is in place to prevent php file uploads to gain malicious code execution. The bash history is provided to you but the recorded commands don’t appear to be related to the attack. Can you find what actually happened? 

Artifacts:

        - bash_history

Q)  What user (other than ‘root’) is present on the server?

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/c763423a-750e-48a8-ae4e-1c53b41a93e5" />


**Answer: daniel**

Q)  What script did the attacker try to download to the server?

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/74987eed-2f1c-4a28-b675-bde81338b164" />


**Answer: linux-exploit-suggester.sh**

Q) What packet analyzer tool did the attacker try to use?

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/1fe1fec8-6220-4460-887f-0d0775c20f50" />


**Answer: tcpdump**

Q) What file extension did the attacker use to bypass the file upload filter implemented by the developer?

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/fa8bae35-56f7-47e5-9da1-25adf89bc867" />


**Answer: .phtml**

Q) Based on the commands run by the attacker before removing the php shell, what misconfiguration was exploited in the ‘python’ binary to gain root-level access? 1- Reverse Shell ; 2- File Upload ; 3- File Write ; 4- SUID ; 5- Library load

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/026530c9-7886-40cf-9800-6377ca81bef9" />


**Answer: 4**
