Game of Active Directory - Notes

This is an intentionally vulnerable Active Directory lab in the theme of Game of Thrones. It's a massive effort put out by the folks at Orange Cyberdefense and is a little finicky to setup on some hardware. I ran into an issue with installing it properly in VMware Workstation Pro. I never did resolve the issues I had which blocked me from installing. I, instead, grabbed a friend and reached out to the cloud hosted lab purveyors on Parrot-CTFs. I had questions that were easily answered by their Discord support team and have been more than happy tinkering about with a friend of mine on our own, separate instances.

This will house my notes on the progression through the lab. I would highly recommend following the creator, Mayfly277's blog: https://mayfly277.github.io/ for a baseline of what you're doing. Be prepared to research on your own as the blog is mostly a walkthrough for the few pages I've engaged with so far. You want to be able to understand **why you're doing what you're doing** for real life engagements, you want to know how an ADCS attack is carried out and the **why** behind the attack so you can teach your peers. This field is nothing without daily, even hourly, learning and having someone explain it fluently is a massive bonus.

# Initial nmap scans
## 192.168.254.10.1 /24
### 
```
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-18 18:10 CDT
Nmap scan report for 192.168.10.1
Host is up (0.15s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT    STATE SERVICE
22/tcp  open  ssh
53/tcp  open  domain
80/tcp  open  http
443/tcp open  https

Nmap scan report for 192.168.10.10
Host is up (0.15s latency).
Not shown: 985 closed tcp ports (reset)
PORT     STATE SERVICE
53/tcp   open  domain
80/tcp   open  http
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
3268/tcp open  globalcatLDAP
3269/tcp open  globalcatLDAPssl
3389/tcp open  ms-wbt-server
5985/tcp open  wsman
5986/tcp open  wsmans

Nmap scan report for 192.168.10.11
Host is up (0.15s latency).
Not shown: 986 closed tcp ports (reset)
PORT     STATE SERVICE
53/tcp   open  domain
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
3268/tcp open  globalcatLDAP
3269/tcp open  globalcatLDAPssl
3389/tcp open  ms-wbt-server
5985/tcp open  wsman
5986/tcp open  wsmans

Nmap scan report for 192.168.10.12
Host is up (0.16s latency).
Not shown: 986 closed tcp ports (reset)
PORT     STATE SERVICE
53/tcp   open  domain
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
3268/tcp open  globalcatLDAP
3269/tcp open  globalcatLDAPssl
3389/tcp open  ms-wbt-server
5985/tcp open  wsman
5986/tcp open  wsmans

Nmap scan report for 192.168.10.21
Host is up (0.15s latency).
Not shown: 997 closed tcp ports (reset)
PORT    STATE    SERVICE
22/tcp  filtered ssh
80/tcp  filtered http
443/tcp filtered https

Nmap scan report for 192.168.10.22
Host is up (0.15s latency).
Not shown: 992 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
1433/tcp open  ms-sql-s
3389/tcp open  ms-wbt-server
5985/tcp open  wsman
5986/tcp open  wsmans

Nmap scan report for 192.168.10.23
Host is up (0.16s latency).
Not shown: 992 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
1433/tcp open  ms-sql-s
3389/tcp open  ms-wbt-server
5985/tcp open  wsman
5986/tcp open  wsmans

Nmap scan report for 192.168.10.111
Host is up (0.16s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
80/tcp open  http

Nmap scan report for 192.168.10.118
Host is up (0.16s latency).
All 1000 scanned ports on 192.168.10.118 are in ignored states.
Not shown: 1000 closed tcp ports (reset)

Nmap done: 256 IP addresses (9 hosts up) scanned in 61.08 seconds

```

### NSlookup on Domain Controllers
```
nslookup -type=srv _ldap._tcp.dc._msdcs.sevenkingdoms.local 192.168.10.10

Server:         192.168.10.10
Address:        192.168.10.10#53

_ldap._tcp.dc._msdcs.sevenkingdoms.local        service = 0 100 389 kingslanding.sevenkingdoms.local.

```

