### ports
`rustscan -a 10.10.212.96 -p 1-65535`
PORT    STATE SERVICE      REASON
22/tcp  open  ssh          syn-ack
80/tcp  open  http         syn-ack
139/tcp open  netbios-ssn  syn-ack
445/tcp open  microsoft-ds syn-ack

### http webserver directory enumeration
`ffuf -u http://10.10.212.96/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt`
found `/css` and `/cloud`
`cloud` directs us to a file upload service. this may be vulnerable to remote file inclusion.

### remote file inclusion (RFI)
let's try uploading a php webshell.
when trying to submit a link to our hosted webshell, we get a message that it needs to be an image.
let's try adding the .jpg extension to see if we're lucky. we are.
we now have a basic shell.

### privilege escalation
`/opt/` contains `dataset.kdbx`. download it to kali via python http server and try cracking password with john.
success. after opening the database we find the password to sysadmin user.

### sysadmin user
now we can submit the first flag. we also get access to the `scripts` directory. upon inspection we see that the file `scripts/script.php` is owned by root, meaning it runs with root privileges. the file contains some code that makes a backup. lets insert a reverse shell.

### root

