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
  ``


<img width="900" height="600" alt="image" src="https://github.com/user-attachments/assets/48bb0305-dd96-4b7a-86dd-61287653267f" />

Now that we have verified the network configuration, the vulnerable machine's IP address is (192.168.38.137).

---

## 2-Scanning & Enumeration  

- At this step, we will use the popular tool Nmap with the following options:

  ```bash
  sudo nmap -sV -sS -sC -Pn -p- -T4 192.168.38.137



<img width="1008" height="770" alt="image" src="https://github.com/user-attachments/assets/003b9f37-7d28-4e52-8a1b-de0811663909" />  

- We identified the following open ports: **22, 80, 111, 139, and 443**. We can now begin our attack methodology by identifying the services and their versions and checking for any known exploit. Since **SMB (port 139)** and **HTTP/HTTPS (ports 80 and 443)** are common attack surfaces, we will focus our initial enumeration on these services.




  
