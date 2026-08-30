# SIEM Detection and Monitoring Lab

## Objective:

To set up a working security monitoring lab using a Wazuh SIEM (Security Information and Event Management). A Windows target machine is used to link to a security dashboard such as Wazuh, to generate system logs and detect
automated brute-force password attacks.

## Tools Used:

- Wazuh SIEM (Central Security Dashboard)
- Windows 11 (Victim target machine)
- Hydra
- Kali Linux


## Steps Taken:

- Booted the SIEM engine and logged into the Wazuh terminal and got its IP using ```ip a```.
- Installed a new Windows 11 VM and bypassed the Microsoft account sign-in using and ```start ms-cxh:localonly``` in cmd. Then I got the Windows IP.
- Opened up the Wazuh dashboard in the Windows VM browser at `https://198.162.1.65`, copied the generated sensor script and pasted it in Powershell (As an admin) to start the tracker.
- Turned on remote desktop on the VM.
- Then I finally executed the attack in a Kali Linux terminal by running a brute-force attack against the Windows remote desktop protocol port using Hydra for a minute to generate logs.

```bash
hydra -l Adminstrator -P /usr/share/wordlists/rockyou.txt rdp://192.168.1.65 -vV
```


<img width="910" height="679" alt="Screenshot 2026-08-30 052716" src="https://github.com/user-attachments/assets/65216871-1056-4466-9d00-ab62037e2dd0" />



## Results:

- The Wazuh dashboard chart showed massive, vertical spikes from 0 right when the Hydra hit.

<img width="1771" height="705" alt="Screenshot 2026-08-30 052243" src="https://github.com/user-attachments/assets/150d8832-11ef-478a-a6ee-001651b76c4e" />

- The SIEM monitored the complete session and logged successful access events when password thresholds matched.


## What I learned:

- Without monitoring tools, any hacking attempts made by attackers will go unnoticed and installing an agent like Wazuh allows you to capture data in the background.
- A single failed login looks harmless but if a hundred failed logins come from a single IP address in a short span of time, it is an obvious attack. SIEM's allow security teams to look back and see how the breach started
  as well as backtrack to find the attacker's information.
- Hacking tools leave behind a digital footprint. Reading the raw log alerts show crucial pieces of evidence like IP addresses, the time of attack and targetted usernames.







