# SIEM Detection and Monitoring Lab

## Objective:

To set up a working security monitoring lab using a Wazuh SIEM and see whether a brute-force attack against Windows machine's Remote Desktop (RDP) Login would show up as detectable activity in a SIEM.
This maps to MITRE ATT&CK T1110 Brute force.

## Environment

- Wazuh - The Dashboard/SIEM, collecting alerts.
- Windows 11 VM - The target, with Wazuh agent installed and RDP turned on.
- Kali Linux - attacker machine, where I run hydra from

## The Scenario:

If an attacker is hammering a login with hundreds of password attempts, that is obviously different from a normal user typing their password once or twice. I wanted to simulate this against an RDP and see what it looks like from a defender's side and what sort of evidence it leaves.


## Steps Taken:

- Booted the SIEM engine and logged into the Wazuh terminal and got its IP using ```ip a```.
- Installed a new Windows 11 VM and bypassed the Microsoft account sign-in using and ```start ms-cxh:localonly``` in cmd. Then I got the Windows IP.
- Opened up the Wazuh dashboard in the Windows VM browser at `https://198.162.1.65`, copied the generated sensor script and pasted it in Powershell (As an admin) to start the tracker.
- Turned on remote desktop on the VM.
- Then I finally executed the attack in a Kali Linux terminal by running a brute-force attack against the Windows remote desktop protocol port using Hydra for a minute to generate logs.
- Watched the Wazuh dashboard while it ran to see what showed up.

```bash
hydra -l Adminstrator -P /usr/share/wordlists/rockyou.txt rdp://192.168.1.65 -vV
```


<img width="910" height="679" alt="Screenshot 2026-08-30 052716" src="https://github.com/user-attachments/assets/65216871-1056-4466-9d00-ab62037e2dd0" />



## Results:

Wazuh logged 43 attempts during the the attack and 2 of the login attempts ended up succeeding, the weak password I used was actually in the wordlist and got cracked.

The alert timeline showed 3 clear spikes, each hitting around 14-18 events which lines up with Hydra running multiple password attempts in parallel threads rather than all at once.

Wazuh tagged this under MITRE'S valid accounts rather than brute force since the attack actually went through, it's treating it as an account compromise and not just a bunch of failed attempts.


<img width="1771" height="705" alt="Screenshot 2026-08-30 052243" src="https://github.com/user-attachments/assets/150d8832-11ef-478a-a6ee-001651b76c4e" />


## Why this is important:

A successful RDP login attempt means full Remote Desktop access to the machine. In a real environment that's enough for an attached to install malware or extract important data. The fact that this happened with a password from a public wordlist like rockyou.txt is why weak password policies are flagged so much in real audits.



## How to stop this:

- Don't allow weak or common passwords.
- Lock accounts after a few failed attempts.
- Set up an alert for a successful login after a burst of failed ones.
- Turn on Multi factor authentication.


## What I learned:

- Without monitoring tools, any hacking attempts made by attackers will go unnoticed and installing an agent like Wazuh allows you to capture data in the background.
- A single failed login looks harmless but if a hundred failed logins come from a single IP address in a short span of time, it is an obvious attack. SIEM's allow security teams to look back and see how the breach started
 as well as backtrack to find the attacker's information.
- Hacking tools leave behind a digital footprint. Reading the raw log alerts show crucial pieces of evidence like IP addresses, the time of attack and targetted usernames.
- Tools like Hydra don't attack in a continuous spike but rather in short bursts.





