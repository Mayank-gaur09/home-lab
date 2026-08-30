# Web Application Vulnerabilities - DVWA

## Objective:

To practice identify and exploit common web application vulnerabilities using DVWA, a web application that has intentional security flaws and vulnerabilities. It is already installed on Metasploitable2.

## Tools Used:

- DVWA (Damn Vulnerable Web Application)
- Crackstation (online hash cracker)



## Vulnerabilities covered


### 1. SQL Injection

**Description:** It is a cyber attack that involves manipulating a website's database by injecting SQL code into an input which causes it to return data it should not.

**What I did:** 

- Injected this input: 1' OR '1'='1 - which returned all 5 users first and last names, since the OR statement will always be true.


<img width="184" height="307" alt="image" src="https://github.com/user-attachments/assets/acf93b9e-aa97-4461-953b-6e55d50df06f" />



- Injected this input: ' UNION SELECT user, password FROM users - which returned every username and it's hashed password (MD5).


<img width="348" height="306" alt="image" src="https://github.com/user-attachments/assets/e7cfc38c-196e-4dd8-99fe-ca5739b20cd2" />



- Cracked the hash passwords using the web tool Crackstation.

1. 5f4dcc3b5aa765d61d8327deb882cf99 - passowrd
2. e99a18c428cb38d5f260853678922e03 - abc123
3. 8d3533d75ae2c3966d7e0d4fcc69216b - charley
4. 0d107d09f5bbe40cade3de5c71e9e9b7 - letmein
5. 5f4dcc3b5aa765d61d8327deb882cf99 - password




**What I learned:** 
- How 'UNION SELECT' extends a query to pull data from a completelydifferent table than intended





