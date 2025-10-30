# [IN-PROGRESS] 8 | Bug Bounty
## Introduction
Now it's time to identify and remediate bugs! Imagine you're part of a Bug bounty program. Find and remediate 5 vulnerabilites on the password manager, use the Bug Bounty Template. To complete this task I used Docker to run the application, Postman for API testing, ZAP for vulnerability scanning and VS code for editing the insecure code. The overall goal of this lab is to get a better understanding of web application security measures and practice hardening techniques for a more secure environment.

## Steps
V1. Insufficient Session Expiration
Open login.php in VS Code
At the top of the PHP block, before session_start(); add the following line to set a short expiration time for session cookies
Call session_start(); immediately after that

In authenticate.php, add this logic to the top of the file for a session timeout:

Save both files
Rebuild and restart the app to apply new changes
Log in again, leave the session idle for 30 seconds. When the page is reloaded, I’m redirected back to the login screen with my session invalidated

V2. Unauthorized CRUD Access to Vault Passwords
Reproduce:
Build Docker, login with a non admin account
Navigate to any vault by clicking on “Vaults” in the nav bar and choose “View Vault”
Inspect the browser for the vault’s vault_id.
Log out of the application.
Use cURL to send a POST request
curl -X POST https://localhost/vaults/vault_details.php \
  -d "username=attacker&website=evil.com&password=hacked&vault_id=1" \
  -k
The command returns a rendered HTML from vault_details.php, confirming a vulnerability.
Reload the vault to confirm the new password entry you added through cURL is now listed. I didn’t see the changes the first time. After examining the vault_details.php I found I used the wrong field name. I reran the POST request with the correct field name
curl -X POST https://localhost/vaults/vault_details.php \
  -d "addUsername=attacker&addWebsite=evil.com&addPassword=hacked&addNotes=pwned&vaultId=1" \
  -k
Reload the vault (https://localhost/vaults/vault_details.php?vault_id=1) and look for a new password entry with username (attacker), website (evil.com). Now it appears, meaning I’ve confirmed unauthorized write access to the vault.

Remediate:
Open vault_details.php in VS code.
Add a session authentication check to the top of the file, after the php tag.

Inside the Add Password and Edit Password block right 
after $vaultId = $_POST[‘vaultId’]; add permissions checks

Inside the Delete Password block after after $vaultId = $_POST[‘vaultId’]; add permission checks

Save the file and recompose or refresh Docker.
Now when I load https://localhost/vaults/vault_details.php into the browser, I do not have access unless I login.

V3. Missing CSP Threat
Reproduce:
Navigate to ZAP framework and run an attack on the localhost Docker to scan for vulnerabilities. It shows a CSP Missing Warning.

Run the script  <script>alert(1);</script> in the vault search bar, see a popup alter saying 1. This confirms a successful XSS attack.
 
Remediate:
Open index.php and vaults/index.php in VS code.
To add a passive defense for the browser, add a CSP header to the meta tag of both files

To allow execution of inline scripts with the strict CSP header, add a nonce-YYYY tag in vaults/index.php to create exceptions. 
After implementing these changes, save the php files and reconfigure Docker.
Retry the XSS attack, it should be unsuccessful.The current CSP setup mitigates some XSS risks but is weakened by the ‘unsafe-inline’ and static nonce(‘YYYY’). For stronger protection we could remove ‘unsafe-inline’, generate a dynamic nonce per request and serve the CSP in a HTTP header instead of a meta tag to ensure it applies before scripts upload.

V4. No Anti-CSRF tokens
V5. HTTP only flag not set on cookies
No Anti-CSRF tokens were found in the login.php HTML submission form. A cross-site request forgery is an attack that involves forcing a victim to send an HTTP request to a target destination without their knowledge or intent in order to perform an action as the victim. The CWE connected to this bug is CWE 352: Cross Site Request Forgery. 
Associated CWEs:
https://cwe.mitre.org/data/definitions/352.html
Severity:
Medium according to the ZAP severity alert. There is potential for security breaches if left unaddressed. 
Description:
The expected behavior is that all state changing HTML submission forms like login should include a unique, cryptographically secure anti-CSRF token. The server must validate this token before processing the request to ensure that the action was intentionally initiated by the user and not a malicious suspect. The observed behavior is that the HTML form code in login.php is <form action="login.php" method="post"> and there is no CSRF token field. The login form lacks a hidden CSRF token field and submitting the form without a token doesn’t trigger server side validation errors. The asset involved is the login.php (login handler) and the authentication forms are critical entry points. For the impact analysis, an attacker could potentially craft a malicious page that auto submits a forged login request when visited by a logged in user. Then if the victim is authenticated, they could change the victim’s password and give the attacker unauthorized access. 
Proof of Concept (PoC):
Use ZAP on https://localhost/vaults . Look at alerts and you will see it. You can also look at login.php and go down to the HTML submission form.

Affected Components:
Web App, login.php file 
Recommendations:
Generate a secure CSRF token like 
if (empty($_SESSION['csrf_token'])) {
    $_SESSION['csrf_token'] = bin2hex(random_bytes(32));
}
Embed the token in the form like 
<input type="hidden" name="csrf_token" value="<?php echo $_SESSION['csrf_token']; ?>">
    <!-- other form fields -->
</form>
Validate the token on submission in the post data like
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    if (!isset($_POST['csrf_token']) || !hash_equals($_SESSION['csrf_token'], $_POST['csrf_token'])) {
        // Invalid CSRF token
        http_response_code(403);
        exit('Invalid CSRF token');
    }
  // Continue processing login
}



V5. HTTP only flag not set on cookies

HTTP only flag is not set on cookies in login.php.Cookies in login.php don’t have attributes with the HttpOnly flag, which means that they can be accessed by JavaScript. If we used XSS, we could get information from the cookie.
Associated CWEs:
https://cwe.mitre.org/data/definitions/1004.html 
Severity:
The severity rating for this issue is deemed to be:
Severe: Significant impact on security, because if there’s sensitive information in the cookie, it can be accessed. In this cookie, since it determines whether the user isSiteAdministrator, the cookie information can then be accessed in the browser to change isSiteAdministrator to true. Therefore, this is a severe issue.
Description:
The cookie that controls access and permissions in our password manager is not secure and the httponly flag is not set.
Expected behavior: The cookie should have attributes for httponly and secure set to true.
Observed behavior: The httponly and secure flags are not set to true, so the cookie can be manipulated in the browser and confidential information can be stolen from the cookie.
Asset: The asset involved is the browser, which is where cookies are displayed.
Projected Risk: The lack of security with the cookie means that we can access the password manager even if we are not the administrator because the cookie is not secure and does not have the httponly flag. The risk is that anyone can write JavaScript to access the cookie’s information.
Proof of Concept: 
After login, open Console
Run document.cookie = "isSiteAdministrator=true; path=/"; to set your admin cookie to true
You can see the cookie set to true even if you are not the admin
OR use ZAP to spot the vulnerability


Affected Components: 
login.php
Recommendations: 
Add flags for ‘secure’ and ‘httponly’

## Reflection


<img width="50%" alt="image" src="https://github.com/user-attachments/assets/7b521eae-ba5a-4092-ab01-76db8d6dd782" />
