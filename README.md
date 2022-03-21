# GreyHack Script
Collection of scripts for the game GreyHack (Steam)

## [7scan.src](7scan.src)
> Usage: 7scan [ip address]

This script will show you all information for the given ipAddress
```
running /bin/7scan
checking IP: 159.38.210.37
running smtp_user_list on port 25 ...
root
Kampinic
Olin
running nmap ...
25  open  smtp  1.0.5  192.168.1.2  
21  open  ftp   1.3.9  192.168.1.2  
22  open  ssh   1.8.8  10.0.20.2    
running scanrouter ...
kernel_router.so : v2.1.6
No firewall rules found!
running whois ...
Domain name: ar-tvbce.net
Administrative contact: Allianora Tmard
Email address: Tmard@ressono.org
Phone: 342558625
```
## [7fastdecipher.src](7fastdecipher.src)
> Usage: 7fastdecipher [user:password]

With this small script, you can decipher one password
```
7fastdecipher root:437cb55c5w2461a657cc3025s67def82
[###################################]==[ 100% ]
user: root password: secretword
```

## [7ports.src](7ports.src)
> Usage: 7ports [serviceName] [serviceVersion]
> ServiceNames: http / ssh / ftp / smtp / bank_account / repository

This script generates valid ipAddresses where given port or version is open (or all)
Example to search for any open ports
```
running /bin/7ports x x
start searching for service: x with version: x ...
128.127.27.230: smtp (1.1.8) 25
128.127.27.230: ftp (1.2.2) 21
128.127.27.230: http (1.5.6) 80
128.127.27.230: ssh (1.2.4) 22
124.68.56.113: http (1.3.0) 80
25.219.147.92: http (1.7.1) 80
18.225.109.242: http (1.4.4) 80
25.6.200.140: ssh (2.7.3) 3452
222.136.92.143: ssh (2.6.7) 22
89.233.200.141: http (1.2.5) 80
36.123.179.224: ssh (1.8.4) 22
36.123.179.224: ftp (1.0.7) 21
109.90.230.130: http (1.2.3) 80
99.240.12.229: ssh (1.1.6) 22
99.240.12.229: http (1.2.2) 80
99.240.12.229: ftp (1.5.2) 21
```

Example to search for only open ssh ports
```
running /bin/7ports ssh x
start searching for service: ssh with version: x ...
198.176.126.164 ssh (2.4.7) 22
177.40.72.90 ssh (2.7.7) 22
67.88.249.125 ssh (2.5.0) 22
118.126.72.194 ssh (1.1.1) 3442
62.216.233.145 ssh (2.4.6) 22
186.22.233.188 ssh (1.1.9) 22
220.93.182.47 ssh (2.4.5) 22
220.93.182.47 ssh (2.2.6) 31820
```

## [7ssh.src](7ssh.src)
> Usage: 7ssh [user@password] [ip address] [(opt) port]

This script is not fully automated!! (Read description below)
- script connects to the remote server, deciphers all banks informations and stores them on your server.
  - DEFAULT: /home/[user]/Downloads/cracked/bank_informations
- It also install a rshell-backdoor, if configured right. This part is not automated, so you need to have the [rshellbackdoor.src](template/rshellbackdoor.src) on the right place.
  - DEFAULT: /home/[user]/Downloads/rshellbackdoor
  - SEE: [template/rshellbackdoor.src](template/rshellbackdoor.src) (works only if an rshellServer is installed in the same network as you run the script, never use it on homeComputer!!)
- at the end it copys an system.log to the remote to remove all traces, you need an empty system.log in
  - DEFAULT: /home/[user]/Downloads/system.log

The script has 2 other functions which are disabled cause of no need for me.
- GetPasswd: stores passwords in: /home/[user]/Downloads/cracked/passwd_[ipaddress]
- GetLibs: BUG: you need to make a folder in  /home/[user]/Downloads/libs (no time to fix it at the moment)
```rust
// START function(s) you want to use
//GetPasswd() // get all passwd passwords
GetBank() // get all bank passwords
//GetLibs() // for lib colleation (needs a lot of diskspace)
InstallRshell() // install rshell-backbone

```

Example output
```
running: /bin/7ssh root@secretword 220.93.182.47 22
Connecting to: 220.93.182.47 ...
collecting bank informations ...
all collected informations will be stored in:
/home/root/Downloads/cracked/bank_informations
[###################################]==[ 100% ]
user: Htn02h74
password: brrdin
[###################################]==[ 100% ]
user: AJEStgej
password: frtdator

connecting to rshellserver: 256.73.231.213 ...
100%	743.5 KB/s	0 sec (4.9 MB of 4.9 MB copied)
100%	685.2 KB/s	0 sec (6.1 MB of 6.1 MB copied)
Processing...
backdoor successful installed
cleanup /var/system.log ...
100%	688.5 KB/s	0 sec (2.1 MB of 2.1 MB copied)
Processing...
... connection to 220.93.182.47 closed
```

## [7attack.src](7attack.src)
> Usage: 7attack [ip-ipAddress] [port]

- [ ] BUG: if 7ssh is not installed it keeps going (loop) -> TBD: inform user and exit
- [ ] BUG: if system.log is not on right place it keeps going (loop) -> TBD: inform user and exit

This Script scans the port for exploids and trys them all till one works.
it then gets the root password, and starts /bin/7ssh to get all the needed informations.

This script needs 7ssh.src (/bin/7ssh) to work as planed!
If you plan to run an other command after you got the remote root password you need to change this part of the line
For exmaple if you only want a root shell on the remote. Change /bin/7ssh to /bin/ssh here:
```lua
localShell.launch("/bin/7ssh", "root@" + password + " " + ipAddress + " " + port) // script you want to start
```

INFO: ``` error: can't change password for user ***. Passwords must be alphanumeric.```
i dont use the exploid to set a new password for a random user, since i cant read the username where the password is set, and there for i cant use this exploid in combination with 7ssh.
you can set a password, but if doing this, this will end the attack, cause it was successful and in wont try all other exploids.

```rust
result = metaLib.overflow(memoryZone, bufferString)
```

to:
```rust
result = metaLib.overflow(memoryZone, bufferString, "new password here")
```

Example with 7ssh installed:
```
running: /bin/7attack 220.93.182.47 31820
scanning libs ...
[###################################]==[ 100% ]
trying: memoryZone: 0x34105FAD bufferString: mizetreelis ...
Searching required library init.so => found!
Starting attack... failed!
Required init.so installed library at version >= 1.2.8. Target installed version is: 1.1.6
trying: memoryZone: 0x34105FAD bufferString: tancessof ...
Searching required library kernel_module.so => found!
Starting attack... failed!
Required kernel_module.so installed library at version >= 2.1.7. Target installed version is: 1.9.9
trying: memoryZone: 0x514B3D16 bufferString: stancess ...

Starting attack...success!
Privileges obtained from user: guest
trying: memoryZone: 0x514B3D16 bufferString: thismax_c ...
Searching required library init.so => found!
Starting attack... failed!
Required init.so installed library at version >= 1.2.5. Target installed version is: 1.1.6
trying: memoryZone: 0x7E4F613 bufferString: olor_ ...

Starting attack...success!
Privileges obtained from user: Agheatio
decipher root password ...
[###################################]==[ 100% ]
running: /bin/7ssh root@Ermand 220.93.182.47 31820
Connecting to: 220.93.182.47 ...
collecting bank informations ...
all collected informations will be stored in:
/home/root/Downloads/cracked/bank_informations
[###################################]==[ 100% ]
user: SnMQPzjW
password: musty
[###################################]==[ 100% ]
user: 8nc6bo2Y
password: kenni

connecting to rshellserver: 256.73.231.213 ...
100%	720.3 KB/s	0 sec (4.9 MB of 4.9 MB copied)
100%	748.0 KB/s	0 sec (6.1 MB of 6.1 MB copied)
Processing...
backdoor successful installed: 1
cleanup /var/system.log ...
100%	689.1 KB/s	0 sec (2.1 MB of 2.1 MB copied)
Processing...
... connection to 220.93.182.47 closed
```
