# W3 | Encryption

## Introduction  
Now that we’ve seen how unencrypted traffic can be intercepted, it’s time to take a proactive step toward security. In this lab, we’ll **secure HusKey Manager with HTTPS** by creating and signing our own SSL/TLS certificate. By deploying HTTPS locally, we’ll understand how encryption protects data **in transit**, and why modern applications must never expose sensitive info in plaintext.

You’ll:
- Generate and sign your own SSL certificate with **OpenSSL**
- Configure **Nginx** to serve your site over HTTPS
- Inspect encrypted traffic using **Wireshark**
- Validate how encryption protects confidentiality, even under MITM conditions

By the end, you’ll see how cryptography upholds **Confidentiality** in the CIA triad—and why encryption alone still isn’t enough.

## Steps  

1. CD into the `certs` folder in your terminal.  
2. Run the first command to generate a certificate signing request.  
3. Use the CA to sign the certificate with the second command.  
4. Use `ls` to confirm `localhost.key`, `localhost.crt`, and `localhost.csr` are created.  
5. Open `docker-compose.yaml` and `nginx-default.conf` in VS Code. Add the necessary certificate mapping and HTTPS configuration.  
6. Save the files and redeploy Docker with `docker-compose up --build`.  
7. Open `https://localhost:443` in Chrome. You’ll be greeted by a browser warning.  
8. Click “Not Secure” in the address bar to inspect the self-signed certificate.  
9. Click “Advanced” and continue to bypass the warning and log in.  
10. Perform an ARP spoofing attack on the network using Bettercap (Mac/Homebrew).  
11. Launch Wireshark, connect to the same Wi-Fi as your victim, and filter traffic using their IP.  
12. Have your partner log in to the password manager.  
13. Monitor traffic and confirm you see **encrypted TLS** packets instead of raw HTTP data.
<img width="80%" alt="image" src="https://github.com/user-attachments/assets/2b38c25a-8592-4746-bcad-66656f875298" />

*The created certificate isn't trusted as the browser doesn't recognize the CA.*

<img width="80%" alt="image" src="https://github.com/user-attachments/assets/707f3b90-c8ff-491d-9f49-7b903821faa4" />

*Login data now encrypted, unreadable in MITM attack.*

## Reflection  

After setting up HTTPS I could no longer see raw HTTP credentials through Wireshark. Instead of raw text passwords, I saw encrypted TLS packets. This confirmed that our web app now protects **Confidentiality**—a core tenet of the CIA triad.

The **presentation layer**—which handles encryption—is now hidden from view, while **data link, network, and transport layers** remain visible. This helped me clearly see where encryption takes effect in the OSI model and how it protects the integrity of data above the transport layer.

I learned that while encryption blocks access to sensitive data, it doesn’t verify identity by itself. Anyone could create a certificate, just as I did. That’s why browsers rely on **Certificate Authorities ** to confirm trust. Without a trusted CA, users will always see warnings, even with encrypted traffic.

From a hacker’s mindset, persistence was key. I had to repeat this lab three times to properly deploy the certification and detect encrypted traffic. This resilience mirrors the essential persistence of attackers.

**💡 Even with HTTPS in place, a compromised CA could issue valid certificates for malicious domains. Securing trust chains is just as important as securing traffic itself.**

<img width="50%" alt="image" src="https://github.com/user-attachments/assets/e75b2e0d-860a-49af-8dd2-b6fd5493801c" />
