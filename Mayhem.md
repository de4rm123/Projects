Artifacts:

        - traffic.pcapng

Preparations:

        - Export HTTP Objects install.ps1, notepad.exe

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/51fb7c50-5038-4d7c-b6d9-85235f1fb9a3" />
<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/70ab4509-aca5-4902-88ef-f416ceb9074a" />


        - Check with VirusTotal(Havoc Framework)

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/4a5824ab-aba4-4bd1-8f0d-21093abc0961" />


Decryption Process:
**tshark -r traffic.pcapng -Y 'http.request.method == "POST"' -T fields -e media.type  | grep deadbeef**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/e737c276-0a52-4555-88b3-b8aa35fad27b" />


        - AES key "946cf2f65ac2d2b868328a18dedcc296cc40fa28fab41a0c34dcc010984410ca"
        - IV "8cd00c3e349290565aaa5a8c3aacd430"

Q) What is the SID of the user that the attacker is executing everything under?

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/2a7cf9e7-4126-41f2-9a5c-45746a7e6bf5" />


**Answer: S-1-5-21-679395392-3966376528-1349639417-1103**

Q) What is the Link-local IPv6 Address of the server? Enter the answer exactly as you see it.

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/ce69cb82-51a3-4c5b-bf43-ab316e7a2a65" />


**Answer: fe80::e134:1b0c:c8d5:3020%6**

Q) The attacker printed a flag for us to see. What is that flag?

<img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/2f50e029-3471-4ea4-b779-d0a582e31dde" />


**Answer: THM{HavOc_C2_DeCRypTing_is_Fun_Fun_FUN}**

Q) The attacker added a new account as a persistence mechanism. What is the username and password of that account? Format is username:password

<img width="1920" height="1080" alt="8" src="https://github.com/user-attachments/assets/06b5a147-0ca2-4479-92df-bbe208346b90" />


**Answer: administrato:WfD3hz3AXZ4n**

Q) The attacker found an important file on the server. What is the full path of that file?

<img width="1920" height="1080" alt="9" src="https://github.com/user-attachments/assets/a62ee835-749a-49b1-a0f9-55cccf3c01e4" />


**Answer: C:\Users\paco\Desktop\Files\clients.csv**

Q) What is the flag found inside the file from question 5?

<img width="1920" height="1080" alt="10" src="https://github.com/user-attachments/assets/31fe46b0-4e48-4179-adfd-8954868e6e99" />


**Answer: THM{I_Can_SEE_ThE_fiL3_YoU_ToOk}**
