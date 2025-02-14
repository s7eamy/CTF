# port scan
```
PORT     STATE SERVICE    REASON
22/tcp   open  ssh        syn-ack
80/tcp   open  http       syn-ack
8080/tcp open  http-proxy syn-ack
```
80 contains a representative webpage
8080 returns 404

# content discovery
<target>:80/#contact reveals two keywords, `Silverpeas` and `scr1ptkiddy`.
on port 80 directory enumeration found only `/assets` and `/images`, subdomain enumeration found nothing
on port 8080 directory enumeration found `/website` and `console`.

after investigating `Silverpeas`, we find it is a collaborative platform usually routed at `/silverpeas`. Port 8080 has a login page for `<target>:8080/silverpeas`.

# exploitation
researching silverpeas exploits we find that the authentication mechanism can be bypassed bi simply omitting the password field in the HTTP post request. after logging in as scr1ptkiddy, we see there are two other accounts: Manager and Administrateur. 
i can't find anything on the admin's account (probably because everything is in french). Manager account, however, yields ssh credentials hidden in notifications. now we have ssh into the server as tim.

# privilege escalation
`id` as tim reveals that we are part of admin group.
this allows us to access possibly sensitive data. for example `/etc/passwd` reveals that there's another user called tyler.
inspecting logs in `/var/logs` we find that tyler created a postgres database. the password is given in logs.
let's try using the password from logs as tyler's ssh login. it worked.
`sudo -l` as tyler reveals that he can execute all commands as sudo.
`sudo su` gives us root privs.
