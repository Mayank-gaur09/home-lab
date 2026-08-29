# Lab 1: Network Reconnaissance
## Objective of this lab: 

To identify open ports and active services on the Metasploitable 2 target machine to recognize what is exposed before attempting any attacks.

## Tools used:

Nmap

## Steps Taken:

```bash
nmap -sV 192.168.56.101
```

## Results:

The scan revealed multiple open ports, several running outdated and vulnerable services - such as vsftpd 2.3.4 on port 21, which has a well known backdoor vulnerability that can be exploited.


<img width="850" height="600" alt="image" src="https://github.com/user-attachments/assets/24728e44-fda7-4175-8248-54d3931ce084" />


## What I learned:

- Why network reconnaissance is the first phase of any security assessment - you cannot attack or defend anything that does not exist.
- Building an isolated lab network (Host-only Adapter) so scanning and exploitation stay fully contained and safe.
