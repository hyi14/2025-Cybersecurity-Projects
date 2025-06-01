# W5 | Access Control

## Introduction  
After implementing encryption and logging, it’s time to dive into **web session security** and how things go wrong when it’s not secured properly.

In this lab, we’ll explore how cookies can be manipulated to impersonate users and how **insecure session management** can grant unauthorized access. Then we’ll harden the system by replacing cookies with **secure PHP sessions** and implementing proper role-based access controls.

we’ll:
- Explore dangers of cookie-based authentication
- Replace cookies with **PHP sessions** for secure authentication
- Protect admin only pages using role-based access checks

By the end, we’ll understand how proper session and access control preserves **Authentication** and **Authorization** two essential components of application security.


## Steps
1. Split screen with Lab 5 directions on the right, browser, docker, VS Code on the left.
2. Compose Docker in Terminal with docker-compose up --build
3. Once the website is up and running on the browser, request a new account, fill in information needed
4. Log in as Admin using the admin credentials
5. Explore the additional tabs with Admin privileges: Users, Vaults, Admin
6. In Users tab find the new account just created/requested and check “approve user” box
7. Log out from Admin, log in with new account
8. In the new account create a new vault with 3 passwords, 1 with a .txt file
9. Log out from new account, Log in as Admin.
10. Open browser Developer Tools > Application > Cookies
11. Explore the cookies: isSiteAdmininstator, authenticated, PHPSESSID
12. Alter the cookies from “Admin” to “Jane Doe” and reload page to break authentication, confirm user has changed from the top menu bar that now says “Welcome Jane Doe”
13. Edit login.php, authenticate.php, logout.php, nav-bar.php files in VS Code to replace $_COOKIE with $_SESSION to use the session ID and strengthen the PHPSESSID (secure authentication)
14. Log into new account and edit cookies to check if authentication and authorization is secured by editing cookies to gain Admin access
15. With unauthorized Admin access, add yourself as the owner of the developer and executive vaults
16. Edit admin-authorization.php, logout.php,in VS Code to confirm the user has the isSiteAdmininstator key  and removes once they log out
17. Edit vault-details.php so the SQL query is changed to use the session ID as the vault’s owner instead of cookies
18. Edit nav-bar.php so it checks the session ID to decide whether to display the admin panel or not
19. Edit > Find in Files to replace all remaining _COOKIE with _SESSION
20. Log with new account and try to edit/add cookies to gain Admin privileges, reload page
21. Having secured the password manager I now cannot edit cookies to gain unauthorized access
<img width="80%" alt="image" src="https://github.com/user-attachments/assets/b01ab5b0-fdd4-43dd-9c82-2c1310485010" />

*Logged in as user: Jane Doe by editing browser cookies without password*

<img width="80%" alt="image" src="https://github.com/user-attachments/assets/f57755d8-66be-4e24-bbf5-954f54a0067f" />

*Altered browser cookies to grant Admin access to personal user: Wow*

## Reflection
Changing _COOKIE to _SESSION in the password manager shifted authentication from the Presentation tier -> Logic tier. Originally **authentication data was stored as cookies directly in the user’s browser within the presentation tier, allowing users to bypass authentication by manually altering cookie values**, demonstrated when accessing Jane Doe’s account and Admin panel without proper credentials. 
When changed to use PHP sessions, the authentication now leverages the PHPSESSID cookies that contain only a unique session identifier, while storing the actual authentication data server side in the logic tier. This architectural shift places **authentication state management within the logic tier, protecting it from direct manipulation** by users. The session data remains under the application’s control in the logic tier, requiring calls to modified authentication components that now check whether an authentication variable exists within the current session instead of cookies, making credential forgery more difficult.

We can use **POL** and **Access Control (RBAC, ABAC)** to ensure users are given access only to resources and information needed to do their specific tasks. Based on their roles or attributes, their privileges should be limited. This not only ensures efficiency of tasks (no overwhelming, exposure to non relevant information) it also strengthens security by decreasing the number who have access to sensitive information. **Authorization Model** is another way to manage user access based on organization department/structure and ensure separation of work with secured access. 
Because PHP often defaults to the **Permissive mechanism**, the developer should ensure that the **Strict mechanism** is enforced, and only accept session ID values that have been previously generated by the web application. If web apps don’t filter invalid session IDs before processing, they can potentially be used to exploit other web vulnerabilities like SQL injection, to access and manipulate unauthorized information. The **session ID must be renewed to reflect the privilege changes associated with the user**. This lab demonstrates unsecured JS can access credential information. The cookies of the unsecured password manager (authenticated-Admin, isSiteAdminstrator-1) could be manipulated to gain unauthorized access to Admin privileges. However, after securing the webpage with sessions –which are more secure being stored on the server side– the webpage access didn’t change when we edited the cookies. It’s crucial for session IDs to be regenerated to prevent session fixation attacks. Other ways we can ensure security is using HTTPOnly and same site cookies. Overall I learned that **small gaps that seem insignificant when placed in the wrong hands can become big gaps**. Most people aren’t even aware they can exploit cookies to gain unauthorized privileges to information(myself included). But all it takes is one knowledgeable, sly individual. In the digital age, access to internal information is the biggest asset and risk for organizations and governments.

Authentication and authorization challenge the hacker mindset by **creating boundaries that require creative workarounds**. A committed hacker could exploit cookie based authentication by directly modifying client side values. Yet when security shifted to server side session management it significantly **increased difficulty, forcing hackers to think creatively** and find complex vulnerabilities like session fixation or cross site request forgery rather than simple cookie manipulation to overcome these enhanced protections. Despite the improved security using server side PHP sessions, an adversary could potentially bypass authentication by exploiting SQL injection vulnerability in the login process. The unfiltered SQL query SELECT * FROM users WHERE username = '$username' AND password = '$password' allows an attacker to add malicious input like ' OR '1'='1 for both username and password fields, potentially authenticating as the first user in the database (like the admin) without knowing actual credentials.

<img width="50%" alt="image" src="https://github.com/user-attachments/assets/10a22419-70b8-4e18-bb12-30831d7f74c3" />
