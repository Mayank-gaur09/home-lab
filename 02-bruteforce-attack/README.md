# Credential Brute-Force Attack

## Objective:

To attempt to obtain the login credentials of the target machine (Metasploitable2) for its FTP service using Hydra, by discovering a password for a known username, and a username for a known password.

## Tools Used:

Hydra

## Steps Taken:

I initially tried SSH Brute-Forcing but was faced with a compatibility error since the modern Kali Linux does not support old MAC algorithms used by Metasploitable2's SSH server, which made me switch to FTP instead.
I also tried brute-forcing the entire rockyou.txt against the FTP service which was extremely slow, so I used a smaller curated wordlist instead for the password and username using 'nano'.

**To find the password** (known username, list of passwords)**



​```bash
hydra -l msfadmin -P password-list-test.txt 192.168.56.101 ftp
​```



**To find the username** (known password, list of usernames)**

```bash
hydra -L username-list-text.txt -p msfadmin 192.168.56.101 ftp
```



## Results:

Both methods successfully identified msfadmin as the credentials which shows how quickly weak/common credentials can be cracked using automated tools.


**Method 1**


<img width="1262" height="186" alt="image" src="https://github.com/user-attachments/assets/a7858f0d-b863-494c-8fbf-3350d84ae7e5" />



**Method 2** 


<img width="1258" height="186" alt="image" src="https://github.com/user-attachments/assets/08b89f46-4abb-4b91-a3b8-f35bcb76e706" />



## What I learned:

- Hydra commands for brute-forcing in either direction, such as lowercase and uppercase letters.
- Why brute-forcing an entire large wordlist against a service is not practical and using a shorter wordlist is a smarter approach.
- That the use of default/common credentials like password or msfadmin is a real world security concern and this is why changing default passwords is a basic but necessary security practice.
- How adapting an approach when something does not work as intended is a normal, necessary part of security testing.

















