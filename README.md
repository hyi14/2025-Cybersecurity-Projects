# W1 | Environment Setup
## Introduction
**Imagine this scenario**: You’re part of a small internal team using a password manager—**HusKey Manager**—for day-to-day work-related password storage. Until now, it’s been an internal tool, but your boss sees bigger potential: they want to turn HusKey Manager into a public-facing consumer product by the end of the quarter.

However, as someone familiar with the system, you know that in its current state, there are numerous security vulnerabilities that must be addressed before the public can safely use it. Over the next weeks, you’ll apply what cybersecurity concept you're learning to:
- Identify and test vulnerabilities
- Remediate critical security flaws
- Ensure the web application maintains full functionality as it's hardened

By the end of the project, your mission is clear: leave behind a **more secure, production-ready version of HusKey Manager**—built for public use.

Ready? Let's go!

## Steps

1. First off, I cloned the HusKey Manager folder repo to my device.
2. Next, to setup the adequete environment for our web application I installed Docker, an isolated "sandbox" environment useful for developing and testing to run applications regardless of an end user's operating system/machine.
3. Inside VS Code I opened the folder and created a .env file to contain the environment variables required to deploy the HusKey Manager.
4. Once everything was set, I deployed the HusKey Manager in Docker and explored the website in Chrome.
<img width="100%" alt="image" src="https://github.com/user-attachments/assets/00e8c000-34cb-4ea1-9827-2dcd32effd8a" />

*Docker environment with three separate images*

<img width="100%" alt="image" src="https://github.com/user-attachments/assets/1240209e-6eb6-4c55-a713-d33941f0550b" />

*Vault page in deployed Huskey Manager*

## Reflection
I often find software setup to be more tedious than the actual applications, Docker being no exception. Yet possibly due to the friendly whale logo, the struggle was filled with more positivity and curiousity. Being new to the "containerization" concept, I leveraged Gemini and Youtube tutorials to learn more about how Docker works. In short the three main images are a MySQL server for handling the datbase, Nginx server to forward client requests to the appropriate backend server and PHP server to execute server side code and communicate directly with the database. Although the HusKey Manager is a very basic password manager, I'm excited to "hack the system", exploiting and patching it!

<img width="50%" alt="image" src="https://github.com/user-attachments/assets/fce71d7a-8919-4a2e-ae0f-a508c28923c2" />

*Yes we got our tools! Now time to rock the project*
