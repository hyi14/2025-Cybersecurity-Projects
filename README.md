# W2 | MITM
## Introduction
You’ve just set up your development environment—but before HusKey Manager goes public, it’s time to test how secure your network really is. Let's become a script kiddie to see just how easy it is to intercept unprotected data.
Using a **man-in-the-middle (MITM) attack**, we’ll explore what happens when a web app doesn’t encrypt traffic properly. You’ll:

- Practice **ARP spoofing** to reroute traffic
- Use **Wireshark** to analyze network packets
- Discover the risks of missing **HTTPS encryption**

By the end, we’ll see firsthand why secure networking is critical to protecting sensitive information. Ready to eavesdrop like an ethical hacker? Let’s dive in.

## Steps

1. Install Homebrew, Bettercap and Wireshark (For Mac users, download ChmodBPF).
2. Open Wireshark and select the same Wifi network as the victim's device. This will show all network packets being sent to and from the victim's computer. While monitoring their traffic, have the victim log into their password manager.
3. Apply a filter to match the source IP address with the victim using ip.src == VICTIM_IP
4. For more organization, I sorted the packets by protocol. Since the victim connected to the unencrypted site of HTTP, we can look for packets that use that protocol.
5. Looking through the packets I see one that uses the POST header to login. Clicking and reviewing the details, I can see the victim'g login information in clear text.
<img width="80%" alt="image" src="https://github.com/user-attachments/assets/ae19fb9f-58e2-442b-9e4c-d946def64d6b" />

## Reflection
By spoofing the ARP table entries by matching my machine's MAC address with the gateway's IP address, I was able to redirect the victim's encrypted traffic toward me. Easily gaining access to their personal login credentials I realized how with the **lack of HTTPS or encrypted tunnels could cause greater harm**. Using the victim's credentials I could exploit the password manager and gain internal access. Acknowledging the simplified MITM attack and insecure system, it was still suprising how a single command "sudo bettercap -eval "set arp.spoof.targets <victim IP>; arp.spoof on" could give access to a whole packet network.

This attack violated **Confidentiality** as it allowed unauthorized interception and viewing of private communications that should have remained between the victim and the intended server. **Integrity** was also compromised since the attacker had the ability to modify packets, altering data being transmitted between victim and server.

Ways to mitigate this types of attack could be sending honeypots as traps or using crowdsourcing certificates to detect unexpected certficates issued to our domain.

<img width="50%" alt="image" src="https://github.com/user-attachments/assets/5ba51720-eef8-45f0-af58-4ddcc4c4393d" />
