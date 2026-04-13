Scenario:

 The Account Executive called the SOC earlier and sounds very frustrated and angry. He stated he can’t access any files on his computer and keeps receiving a pop-up stating that his files have been encrypted. You disconnected the computer from the network and extracted the memory dump of his machine and started analyzing it with Volatility. Continue your investigation to uncover how the ransomware works and how to stop it! 

Artifacts:

        - infected.vmem

Preparations:

        - vol.py -f infected.vmem imageinfo

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/3f072d4c-5693-4d44-8be8-39916c2a2a45" />


Q) What is the name of the suspicious process?
**vol.py -f infected.vmem --profile=Win7SP1x86 psscan**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/7ef97486-79a1-4fdb-a5e0-0fdb4fd60f5e" />


**Answer: @WanaDecryptor**

Q) What is the parent process ID for the suspicious process?

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/8b583676-173f-4a92-ad37-6a80856c8608" />


**Answer: 2732**

Q) What is the initial malicious executable that created this process?

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/8b583676-173f-4a92-ad37-6a80856c8608" />

**Answer: or4qtckT.exe**

Q) Which process is  used to malicious executable in question 3?

**vol.py -f infected.vmem --profile=Win7SP1x86 psscan | grep 2732**

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/97d13ca5-45af-46c3-9b36-e6ddab1f3776" />


**Answer: taskdl.exe**

Q) Find the path where the malicious file was first executed

**vol.py -f infected.vmem --profile=Win7SP1x86 filescan | grep "or4qtckT.exe"**

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/7f192543-9f2c-41fe-b114-2b09dd356a6f" />


**Answer: C:\Users\hacker\Desktop\or4qtckT.exe**

Q) Can you identify what ransomware it is?

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/7066d6f5-2d8b-46dc-a939-9001e3b35f7e" />


**Answer: WannaCry**

Q) What is the filename for the file with the ransomware public key that was used to encrypt the private key?

**vol.py -f infected.vmem --profile=Win7SP1x86 filescan | grep ".eky"**

<img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/9b75b7cf-19ac-4d68-974e-61240df7abdb" />


**Answer: 00000000.eky**
                        
