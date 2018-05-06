# Metasploit Course Notes

Welcome to the [Metasploit](https://www.metasploit.com/) course notes for the Udemy course [Learn Website Hacking / Penetration Testing From Scratch](https://www.udemy.com/learn-website-hacking-penetration-testing-from-scratch/)

## Information gathering

First things first. You must know your target! Here are some tools to gather information you need:

* [WhoIs Tools](http://whois.domaintools.com/) - Find information about the owner of the target site
* [Netcraft Site Report](https://toolbar.netcraft.com/) - Find information about the technology used
* [BuiltWith](http://builtwith.com/) - Find information about the technology used
* [Robtex DNS Lookup](http://robtex.com) - Comprehensive DNS information about the target site
* [Subdomain Discovery](https://github.com/guelfoweb/knock) - Subdomain scanner (requires git clone)

```
# Do this in Kali Linux :)
git clone https://github.com/guelfoweb/knock.git
cd knock
sudo python setup.py install
knockpy -h
knockpy rotati.com
```

## Discover exploits

Once you have the information about the target its now time to check what exploits are possible. Here are some online tools to help with that:

* [Exploit DB](https://www.exploit-db.com/) - lists publicly known exploits for many different software products
* [IP Search](https://bing.com) - Discover if other sites are running on the same server via an [IP address search](https://www.bing.com/search?q=ip%3A13.250.177.223)