Scenario:

Our SSH server is showing strange library linking errors, and critical folders seem to be missing despite their confirmed existence. Investigate the anomalies in the library loading process and filesystem. Look for hidden manipulations that could indicate a userland rootkit.


**ldd /bin/ls**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/ce07a56b-1c2a-412c-86de-f2aa99062635" />


**sha256sum /lib/x86_64-linux-gnu/libc.hook.so.6**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/0d80d8db-fc60-4179-80a9-d23c899c93bb" />



<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/70fbc790-7ec8-46d1-9e68-6135eb83914d" />


**rm /lib/x86_64-linux-gnu/libc.hook.so.6**
**find / -type d -name pr3l04d_ 2>/dev/null**

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/dd6210a2-8a2f-409a-8220-b56c906daee5" />


**cd /var/pr3l04d_**

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/76730872-1bde-4bba-8f39-eda09f6f6f0b" />


**Answer: HTB{Us3rL4nd_R00tK1t_R3m0v3dd!}**
