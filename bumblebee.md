Scenario:

An external contractor has accessed the internal forum here at Forela via the Guest Wi-Fi, and they appear to have stolen credentials for the administrative user! We have attached some logs from the forum and a full database dump in sqlite3 format to help you in your investigation.

Artifacts:
        - access.log
        - phpbb.sqlite3

Q) What was the username of the external contractor?

**SELECT username,user_password FROM phpbb_users;**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/c8d40bc1-f1a4-4538-97b8-b162807e1da2" />


**Answer: apoole1**

Q) What IP address did the contractor use to create their account?

**SELECT username,user_ip FROM phpbb_users;**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/84002e33-b282-4ae5-8f39-b856859131f8" />


**Answer: 10.10.0.78**

Q) What is the post_id of the malicious post that the contractor made?

**SELECT post_id,poster_ip,post_time,post_text FROM phpbb_posts;**

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/6cb3d2f5-0189-4d09-a420-3b040e3aa1bf" />


**Answer: 9**

Q) What is the full URI that the credential stealer sends its data to?

**SELECT post_id,poster_ip,post_time,post_text FROM phpbb_posts;**

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/17bd91e6-2a4b-42bf-ace4-569cb74eaf2c" />


**Answer: http://10.10.0.78/update.php**

Q) When did the contractor log into the forum as the administrator? (UTC)

**SELECT log_ip,log_time,log_operation,log_data FROM phpbb_log;**

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/72bb6c27-5e53-4e20-bbf6-f3c56d768b0c" />
<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/110fc9db-0ef2-4db2-828b-0290081e01a0" />


**Answer: 26/04/2023 10:53:12**

Q) In the forum there are plaintext credentials for the LDAP connection, what is the password?

**SELECT config_name,config_value FROM phpbb_config;**

<img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/989f1c76-31b2-435e-82b0-6ba30ad8d492" />


**Answer: Passw0rd1**

Q) What is the user agent of the Administrator user?

**cat access.log| cut -d "\"" -f 6 | sort | uniq**

<img width="1920" height="1080" alt="8" src="https://github.com/user-attachments/assets/73696b55-258d-4524-ae39-18b36f77b19f" />


**Answer: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.0.0 Safari/537.36**

Q) What time did the contractor add themselves to the Administrator group? (UTC)

**SELECT log_time,log_operation,log_data FROM phpbb_log;**

<img width="1920" height="1080" alt="9" src="https://github.com/user-attachments/assets/373b8680-7bce-4314-8652-e5eadcb0dbd1" />
<img width="1920" height="1080" alt="10" src="https://github.com/user-attachments/assets/0cbeda91-882e-4768-9845-02945b39b37c" />


**Answer: 26/04/2023 10:53:51**

Q) What time did the contractor download the database backup? (UTC)

**cat access.log | grep GET | grep .sql**

<img width="1920" height="1080" alt="11" src="https://github.com/user-attachments/assets/b74e3e02-a0d9-4e56-ab01-03ed940d8f7f" />


**Answer: 26/04/2023 11:01:38**

Q) What was the size in bytes of the database backup as stated by access.log?

**cat access.log | grep GET | grep .sql | cut -d " " -f 10**

<img width="1920" height="1080" alt="12" src="https://github.com/user-attachments/assets/1aef6e00-6b10-4939-95bb-657f3e5863dc" />


**Answer: 34707**
