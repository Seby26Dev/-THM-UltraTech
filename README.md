# -THM-UltraTech

We define the ip
```
IP=xx.xx.xx.xxx.xx
```

# Q1 Which software is using the port 8081?

I used a simple Nmap scan to identify the service running on port 8081.


```
nmap -p 8081 -sV $IP
```

<img width="425" height="48" alt="image" src="https://github.com/user-attachments/assets/6e4702e8-a9dd-4297-adc1-7301a1ee0ac7" />

__R1 = Node.js__ 

# Q2 Which other non-standard port is used? 

Let's scan all the ports with:

```
nmap -sV -sC -oN nmap_scan $IP -p-
```

We discover that port 31331/tcp is open.

<img width="645" height="77" alt="image" src="https://github.com/user-attachments/assets/152e473e-4a4b-4d0a-a4c2-4a87fd10d6d8" />

__R2= 31331__

# Q3 Which software using this port?

As seen in the output below:

<img width="622" height="74" alt="image" src="https://github.com/user-attachments/assets/e6f256ba-5e8e-4203-8fb1-ee72b8368cff" />

__R3 = Apache__

# Q4 Which GNU/Linux distribution seems to be used?

As seen in the output below:

<img width="594" height="80" alt="image" src="https://github.com/user-attachments/assets/7e751a1c-408d-45e8-a9b9-3f52127385f1" />

__R4 = Ubuntu__ 

# Q5 The software using the port 8081 is a REST api, how many of its routes are used by the web application?

I used Gobuster to enumerate directories on port 8081:

```
gobuster dir -u http:/<IP>:8081/ -w /usr/share/seclists/Discovery/Web-Content/raft-small-directories.txt
```

We found 2 ports with status code `200`.

<img width="426" height="56" alt="image" src="https://github.com/user-attachments/assets/017c9272-0d80-43bc-b49a-ea38655255bf" />


__R5 = 2__

# Q6 There is a database lying around, what is its filename?

I used Gobuster to enumerate the web server on port 31331:

```
gobuster dir -u http://IP:31331/ -w /usr/share/seclists/Discovery/Web-Content/common.txt
```


<img width="779" height="182" alt="image" src="https://github.com/user-attachments/assets/67c19cab-09ca-4860-9216-5c4a86957465" />


I found `/js`, which contained a file  `api.js`.


<img width="714" height="672" alt="image" src="https://github.com/user-attachments/assets/d393ee6e-e7b4-482a-9227-b2183069bf44" />

We use this to ping with `ls` and we get the db


<img width="474" height="94" alt="image" src="https://github.com/user-attachments/assets/36c3c6a1-dd3f-41d7-a1fb-296fa4846a19" />

__R6 = utech.db.sqlite__

# Q7 What is the first user's password hash?

We `cat` the `db` and we see the r00t with ther hash

<img width="889" height="106" alt="image" src="https://github.com/user-attachments/assets/b4b12cc6-2a59-4adf-83e4-d08eb81aac88" />

__R7 = f357a0c52799563c7c7b76c1e7543a32__

# Q8 What is the password associated with this hash?

We use `john` to crack the hash 

```
john --wordlist=/usr/share/wordlists/rockyou.txt hash --format=Raw-md5
```

<img width="772" height="159" alt="image" src="https://github.com/user-attachments/assets/8d4444be-029b-48fa-8189-f4a0bf18dc4c" />


__R8 = n100906__

# Q9 What are the first 9 characters of the root user's private SSH key?

In `/robots.txt`, I found a reference to `Sitemap: /utech_sitemap.txt`, which led me to the login page.

<img width="491" height="173" alt="image" src="https://github.com/user-attachments/assets/3c0e03db-e532-45a5-9923-ac104317562c" />


<img width="857" height="883" alt="image" src="https://github.com/user-attachments/assets/4e9c3531-6d86-42ff-9150-1540975c4293" />


We can logg in with r00t:n100906 where we find this : 

<img width="580" height="192" alt="image" src="https://github.com/user-attachments/assets/927a3aba-e2f3-4e6a-8e6a-786a7434698f" />

After we try to login with SSH
```
ssh r00t@IP
```

<img width="519" height="34" alt="image" src="https://github.com/user-attachments/assets/898464b6-ca6e-4bfa-be49-0f87bab11a43" />

There is a privilege escalation technique with docker 

<img width="732" height="51" alt="image" src="https://github.com/user-attachments/assets/e902763c-67cc-4211-b1bd-64d2233fcd71" />

And we obtain root privilage with docker image 

```
docker run -v /:/mnt --rm -it bash chroot /mnt bash

```

<img width="569" height="538" alt="image" src="https://github.com/user-attachments/assets/302ea503-83b3-449a-aee1-81088e6c97bf" />


__R9 = MIIEogIBA__
