# Kioptix-Level-1

lets first descover the network to find our victim machine

netdiscover -r 192.168.1.0/24


We find it! Now lets scan for ports and versions

nmap -sS -T4 -p- -A 192.168.1.104

![image](https://user-images.githubusercontent.com/24206178/154685940-1b082d3c-c4e8-4cc1-a00f-ef29455dc7ac.png)

lets take a little close up!

we find port 80 open so there is a web page

![image](https://user-images.githubusercontent.com/24206178/154685975-3e6e78dd-a3a2-47ac-bdef-8ce85d1d3df4.png)

it is a test page, not much to find

lets try to add https to explore port 443

![image](https://user-images.githubusercontent.com/24206178/154685997-dda1d0e4-2715-4967-8451-fbd41e6f391d.png)

same result

but there is a web page so lets try to find more about it

nikto -host 192.168.1.104

![image](https://user-images.githubusercontent.com/24206178/154686061-bdf13a85-29e3-4d73-ad70-203b44e2b3fb.png)

there is some nice information

+ mod_ssl/2.8.4 appears to be outdated (current is at least 2.8.31) (may depend on server version)
+ OpenSSL/0.9.6b appears to be outdated (current is at least 1.1.1). OpenSSL 1.0.0o and 0.9.8zc are also current.
+ Apache/1.3.20 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.

those three services are outdated, so maybe they are vaulnarable

mod_ssl/2.8.4 - mod_ssl 2.8.7 and lower are vulnerable to a remote buffer overflow which may allow a remote shell    BINGO!!

Thats a good find, a remote buffer overflow is always a good fine.

+ Allowed HTTP Methods: GET, HEAD, OPTIONS, TRACE 

unforunatly there is no PUT or DELETE so no benfit from these

wow wow lets get back a little bit

+ Server: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b

and our stealth scan said

http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b

There is server header information disclosure!  Nice find!!

![image](https://user-images.githubusercontent.com/24206178/154686019-eefd4e36-9975-41ee-87c3-7b8ea289c84f.png)

now lets google!

port 80, 443

Search for Apache 1.3.20

![image](https://user-images.githubusercontent.com/24206178/154686080-6c05fbd8-746b-49b2-b4d5-e0eba9056e72.png)

![image](https://user-images.githubusercontent.com/24206178/154686091-85acdb04-2663-41d8-aa58-e84902caea57.png)

we found some vaulnarabilites but no red ones!
still we will write that up

Remember we are not intersted in brute force attack in a pentration test most of the time
----------------------------------------
last to say about port 443

lets go back to our scan

![image](https://user-images.githubusercontent.com/24206178/154686112-796a0710-6bc8-498f-bca9-f13338f01c8c.png)

Those ciphers are a find!

run a script scan

nmap -sV --script ssl-enum-ciphers -p 443 192.168.1.104

![image](https://user-images.githubusercontent.com/24206178/154686128-e5b9e716-881b-4e07-8268-e3910488b058.png)

![image](https://user-images.githubusercontent.com/24206178/154686140-fddba528-2e49-4c7f-8b60-7b69ecf8f9cc.png)

Creat a chart and add those, so you can tell the agent that he is valunerable to those.

F is the weakest

C is a medium one

----------------------------------------
port 22

Search for OpenSSH 2.9p2

![image](https://user-images.githubusercontent.com/24206178/154686194-89cdabf6-1f53-4c3d-b44e-6002276f6b6e.png)

![image](https://user-images.githubusercontent.com/24206178/154686175-bd107f21-692d-41c7-b2ab-9331facfc84b.png)

## Note

You may or may not find a code to exploit! May be it is just a report, remember that.

![image](https://user-images.githubusercontent.com/24206178/154686256-297d2d0a-78ea-4912-8dd0-b4c99377ed14.png)

There are using OpenSSH 2.9p2 where tha latest version is 8.0

good find!!

Lets use another tool called searchsploit

![image](https://user-images.githubusercontent.com/24206178/154686265-7decf95d-cf45-4239-9233-4b1524020211.png)

way to specific!

![image](https://user-images.githubusercontent.com/24206178/154686230-4f52c7b0-2309-4c84-bfbf-8640ff97f2b7.png)

Now we are talking!


Now lets dig in another port

port 111 RPC

![image](https://user-images.githubusercontent.com/24206178/154686285-9d9b049d-6205-4b2c-95bf-8b71851a4e70.png)

We got a null session using rpcclient. Nice!

lets play around

![image](https://user-images.githubusercontent.com/24206178/154686306-9f7afec9-6c88-4b0e-9574-f2fbc36913a2.png)

it is a Smba server

![image](https://user-images.githubusercontent.com/24206178/154686325-4a60a281-0d5c-42b4-a44e-e0c1e2427160.png)

and we can use smbclient

![image](https://user-images.githubusercontent.com/24206178/154686343-0086c431-881b-4ab8-9877-33d35534f3b4.png)

But we still don't know the samba version

Now we will go to the sweat metasploit

msfconsol

now search for smb

search smb

lets scroll untill we fine what will make version scan for us

![image](https://user-images.githubusercontent.com/24206178/154686390-e4a67791-d4ec-483d-bfed-23519f35d039.png)

We found it, good job so far!  now lets run it

![image](https://user-images.githubusercontent.com/24206178/154686400-a0acc433-cb3f-4153-92da-632923d2e595.png)

![image](https://user-images.githubusercontent.com/24206178/154686367-32d35653-1b0a-46ad-9494-fd8552fa71ff.png)

Samba 2.2.1a,   BINGO!!

![image](https://user-images.githubusercontent.com/24206178/154686413-6b63328c-28b3-4f47-9d43-3bc857669aec.png)

Okay, so there is something called trans2open

search that in our metasploit

![image](https://user-images.githubusercontent.com/24206178/154686446-507d2fd8-3b0a-450d-b6d2-96bc095fb22a.png)

BINGO!

will using the linux one of course

Now we covered all the ports, we are done with the enumeration


Lets exploit boys!!!!

![image](https://user-images.githubusercontent.com/24206178/154686462-7c2a17a8-e5a5-4eeb-a2ac-8a66f0cc4849.png)

![image](https://user-images.githubusercontent.com/24206178/154686473-26b1f4f0-cec0-453a-9f0e-b07e781c9021.png)

It dosn't work, OPSSS!

know we know that there is a velnurability because the session is opened, but it cis closing.

first thing to think of is the payload

![image](https://user-images.githubusercontent.com/24206178/154686494-54fbae97-bb28-48cb-8abf-3af817d841f9.png)

the defualt one is not working

so lets try another one

![image](https://user-images.githubusercontent.com/24206178/154686533-500866f7-b27b-4a46-bd24-93365de89cab.png)

BINGOOOOOOO!!

lets Play!

![image](https://user-images.githubusercontent.com/24206178/154686562-94050d56-47e0-4a41-89f5-881404552c9d.png)

![image](https://user-images.githubusercontent.com/24206178/154686572-8b03e297-195c-4a3b-bd72-5e050b82ca07.png)

![image](https://user-images.githubusercontent.com/24206178/154686581-e86efb3c-2c9c-4189-b803-e2084f120850.png)

![image](https://user-images.githubusercontent.com/24206178/154686628-70be731b-c274-4597-b2f0-c7ed2df0ced7.png)

We are done but lets try another way to run the exploit

get it from exploitdb

![image](https://user-images.githubusercontent.com/24206178/154686595-71914acf-cd0d-441e-9413-056129af24b7.png)

![image](https://user-images.githubusercontent.com/24206178/154686647-d2349ea1-ca59-45cc-b149-89eacbb36943.png)

![image](https://user-images.githubusercontent.com/24206178/154686660-dd51c90d-f73f-4cc6-a5c4-3f120cb0e38b.png)

![image](https://user-images.githubusercontent.com/24206178/154686668-20eef989-0c42-47ba-a186-16077ca0f87a.png)

![image](https://user-images.githubusercontent.com/24206178/154686674-cf3b4a73-62ec-492f-90b7-bdaf35c48367.png)

Thank you!

and by the way, you can find the flag in 

Flag => cat /var/mail/root

# Findings

Found defult webpage on port 80,443

Server header information disclosure port 80,443

Apache/1.3.20 multi vaulnerabilities port 80,443

mod_ssl 2.8.4 Remote Buffer Overflow port 80,443

OpenSSH 2.9p2  Out of date  port 22   --run Brute force attack

Weak ciphers  port 443


Null session on RPC port 111


