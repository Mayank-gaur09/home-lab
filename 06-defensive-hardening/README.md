# Defensive Hardening with an Ubuntu Server
## Objective:

To attack a standard Ubuntu server with baseline tests and then apply hardening measures to the server, then repeat the exact same attacks as earlier to show that hardening reduces the attack surface.

## Environment

- Ubuntu 26.04.1 LTS Server - the target machine
- Kali Linux - where I ran attacks from



## Before Hardening the Server:



### Port Scan


```bash
nmap -sV 192.168.56.104
```


<img width="761" height="197" alt="image" src="https://github.com/user-attachments/assets/ce16f982-3b61-4209-a2b8-1513f229dac9" />


- Showed that port 22 (SSH) was open and responsive which was expected.


###  SSH Brute Force Attack


- Used a small 10-list password against a deliberately weak password (password123) which was cracked pretty quickly.


```bash
hydra -l ubuntu-hardening -P password-list-test.txt ssh://192.168.56.104
```

<img width="982" height="214" alt="image" src="https://github.com/user-attachments/assets/6c7aebb9-2391-4d39-b2e8-79ba82736e1c" />



- Changed the password to a stronger one and the password-list was expanded to 72 realistic/common passwords. 0 of 72 passwords were found but could still be cracked provided there was a bigger list and more time.


```bash
hydra -l ubuntu-hardening -P password-list-test.txt ssh://192.168.56.104
```


<img width="819" height="174" alt="image" src="https://github.com/user-attachments/assets/fb71bd97-9076-4034-89eb-f68e85626c6e" />


### Root Login Attempt

- Rejected after a failed password attempt (no root password was set by default), but the path to root via SSH still existed.

```bash
ssh root@192.168.56.104
```


<img width="299" height="135" alt="Screenshot 2026-08-30 163712" src="https://github.com/user-attachments/assets/0c676ff3-9b37-4ef7-9acd-4a79c47955b0" />



## Hardening the server

### System Update

- Updating the server and upgrading any services so that there are not any exploits.

```bash
sudo apt update && sudo apt upgrade -y
```



### UFW Firewall

- Enabled the UFW firewall on the server but with SSH as an exception.

```bash
sudo ufw allow ssh
sudo ufw enable
sudo ufw status verbose
```

<img width="499" height="284" alt="Screenshot 2026-08-30 170411" src="https://github.com/user-attachments/assets/6b3af34e-e800-4974-b33a-d89c604c69d7" />



### Disabling Password Authentication and Root SSH login

- I disabled password authentication and being able to log into the root of the server via ssh by editing /etc/ssh/sshd_config

```bash
sudo nano /etc/ssh/sshd_config
PermitRootLogin no
PasswordAuthentication no
```


### SSH Key Authentication

- Generated a passphrase protected ed25519 key pair on Kali linux, then copied the public key to the Ubuntu server so it can authenticate using the key rather than the password. Logging in to the Ubuntu server via SSH prompted
for the key's passphrase instead of the account password, disabling password authentication and letting me access the server from Kali.

```bash
ssh-keygen -t ed25519 -C "home-lab-key"
ssh-copy-id ubuntu-hardening@192.168.56.104
```

<img width="623" height="358" alt="Screenshot 2026-08-30 180932" src="https://github.com/user-attachments/assets/e826c9e9-9dc4-4911-8edc-566fa146e053" />


<img width="949" height="674" alt="Screenshot 2026-08-30 180918" src="https://github.com/user-attachments/assets/c6a82b4d-3af5-423c-8480-7d7774e65066" />




### Disabled unnecessary services


- I disabled unnecessary services that were managing hardware and were not required in this VM since every running service is a potential attacking surface.

- A cloud-init config file `/etc/ssh/sshd_config.d/50-cloud-init.conf` was overriding my SSH hardening and I was not being able to disable password authentication. I had to track it down and fix it too otherwise the hardening would have been being bypassed while it looked like it worked.


<img width="807" height="74" alt="Screenshot 2026-08-30 184541" src="https://github.com/user-attachments/assets/c5f87e6f-897a-4155-b294-97ee2710458c" />


## After Hardening - Repeating the Same Attacks

### Port scan

- Port 22 is still shown as open since it was explicitly allowed through the firewall. The other 999 ports now report as filtered rather than closed.

<img width="761" height="206" alt="Screenshot 2026-08-30 185010" src="https://github.com/user-attachments/assets/f7dcbce7-dbd5-443f-83b9-560cf27b4455" />


### SSH Brute Force Attack

- Hydra did not even attempt a single password since `target does not support password authentication`, passwords are rejected and keys are used instead.

<img width="1248" height="151" alt="Screenshot 2026-08-30 185049" src="https://github.com/user-attachments/assets/2fdcea4b-a2c8-4b7e-8adb-20b5026dbea2" />


### Root Login Attempt

- The root login attempt is instantly rejected without a prompt to enter a password, since root login is explicitly forbidden and password authentication is also fully disabled so there's no remaining path to the root via SSH


<img width="447" height="67" alt="Screenshot 2026-08-30 185521" src="https://github.com/user-attachments/assets/3ee41eeb-36b4-4fc2-bb31-b995fe61a02c" />


## Why this is important:

This is basically the same logic behind real-world hardening benchmarks like CIS benchmarks, removing unnecessary attack surface, enforce key based authentication over passwords and firewall everything by default. The brute force attempt here maps to MITRE ATT&CK T1110 Brute Force; disabling password authentication in favour of SSH keys directly closes off any attempts of brute-force attacks.


## What I learned:

- Having a strong password alone isn't a complete defense, even though a weak password was cracked in seconds, but even the strong password was still a target until password authentication was disabled.
- `ssh-keygen -t ed25519` to generate a modern and faster key compared to the default.
- `ssh-copy-id` to install the SSH key onto the server as an authorized key, without requiring a password for each login.
- Paraphrase protecting a private key adds a second layer of protection, even if the key file was stolen.









