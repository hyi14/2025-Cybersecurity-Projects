# W4 | Logging

## Introduction  
You’ve secured your web traffic. But what happens **after someone tries to get in**? In this lab, you’ll set up centralized logging using **Loggly** to monitor user login behavior in real time.

By recording successful/failed login attempts, you’ll start to think like a security analyst: spotting unusual patterns, detecting potential brute force attempts, flagging suspicious accounts before they escalate.

You’ll:
- Integrate **Monolog logging library** into the web app
- Route log data to **Loggly** a cloud based log management platform
- Log failed login attempts and view them in a centralized dashboard

By the end, you’ll see how even a simple log message can play a major role in early threat detection and forensic analysis.


## Steps
1. Go to Loggly to create a free trial account
2. Copy personal token, open the password manager file in VS code
3. Create a new file called loggly-logger.php and paste the code component
4. Add LOGGLY_TOKEN: <your_token> in  .env file
5. Add environment: LOGGLY_TOKEN: ${LOGGLY_TOKEN} in docker-compose.yaml
6. Add include './components/loggly-logger.php' and $logger->warning("Login failed for username: $username"); in login.php
7. Configure password manager website through Docker, login using proper credentials
8. Change time filter on Loggly to see logs
9. Go to password manager website again, login with incorrect credentials
10. Go to Loggly to look for level 300 warning log with error message
<img width="80%" alt="image" src="https://github.com/user-attachments/assets/888559ae-08f6-402d-89b4-b92a2ccf56db" />

*Details of failed login log*

## Reflection
Generally, as a detailed record of system events, logging is crucial for understanding security incidents during attack investigation and post analysis. It provides concrete evidence for conducting digital forensics and helps detect early threats and draw incident response plans before damage increases. Regarding **Confidentiality**, logs are useful for monitoring suspicious behaviour, like a sudden increase in account login requests from an unknown IP address. Such supervision ensures only the authorized have access to the data. Access to the logs itself is also restricted by the user’s role. Being detailed information, logs are typically available to administrators or log readers. For **Integrity**, thorough monitoring allows infrastructure visibility to assess the system’s health and patch any vulnerabilities. Managing the logs in one place also correlates data across various sources. It also serves as proof for adherence to compliance and auditing, safeguarding the accuracy and completeness of the data. Through systematic logging, admins can catch early failures and take action before full damage occurs. By providing real time insight into system operations for maintenance and disaster recovery, **Availability** is kept.

I attempted to add the following logs: Account request, File download, Vault Permission Change. Logging requests in the request_account.php provides visibility to who is attempting to enter the system, helping detect suspicious sign up patterns or potential vulnerabilities early on. Tracking file downloads in download_file.php is important because users could be accessing or withdrawing sensitive data. Maintaining a “trail” ensures any access can be quickly investigated. Logging vault permissions in vault_permissions.php is critical as **changing access controls impacts who can view/manipulate confidential information**. Once the actor gets a hold of an account, they are likely to grant or revoke access to their advantage. Tracking these changes supports accountability.

Some other user actions that should be logged are “Vault create/delete” so the system has **evidence on file** if the vaults were tampered with. Another is “Logout”, or when the user completes session history to help track session patterns. Suspicious activity will be easier to detect if the user has an **unusual or consistent usage pattern**. In short, these additional logs will enhance the organization’s ability to detect threats, trace actions and maintain strong governance over sensitive assets.

From basic operating systems events to application operations, creating logs and monitoring them opened my eyes to how foundational they are to threat detection. With both security and actors/attack approaches becoming increasingly sophisticated, it is often in the systems we take for granted that the small but significant details lie. Because the transparency of logs make it difficult for actors to bypass, they use a victim’s credentials to move about. Even if the user is a normal user, suspicious activity like a sudden increase in emails from the specific address –potential email phishing– could be noticed and taken care of by the admin.

<img width="50%" alt="image" src="https://github.com/user-attachments/assets/c115a6d9-793c-467f-bffd-951754cd1011" />
