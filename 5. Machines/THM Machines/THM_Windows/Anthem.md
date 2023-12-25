### Nmap

```
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=WIN-LU09299160F
| Not valid before: 2023-10-14T11:51:44
|_Not valid after:  2024-04-14T11:51:44
| rdp-ntlm-info: 
|   Target_Name: WIN-LU09299160F
|   NetBIOS_Domain_Name: WIN-LU09299160F
|   NetBIOS_Computer_Name: WIN-LU09299160F
|   DNS_Domain_Name: WIN-LU09299160F
|   DNS_Computer_Name: WIN-LU09299160F
|   Product_Version: 10.0.17763
|_  System_Time: 2023-10-15T12:12:41+00:00
|_ssl-date: 2023-10-15T12:13:50+00:00; -5h30m15s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -5h30m15s, deviation: 0s, median: -5h30m15s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 104.27 seconds
```

### HTTP

- HTTP took some time to load
- Anthem.com - this domain name was written on the site
- Found few usernames on the site:
```
AUTHOR
James Orchard Halliwell

#### [Jane Doe](http://10.10.71.4/authors/jane-doe/)
```

- We also get this 
```
Website: [THM{L0L_WH0_D15}](http://10.10.71.4/authors/jane-doe/THM%7BL0L_WH0_D15%7D)
```

- Found this in `robots.txt`
```
UmbracoIsTheBest!

# Use for all search robots
User-agent: *

# Define the directories not to crawl
Disallow: /bin/
Disallow: /config/
Disallow: /umbraco/
Disallow: /umbraco_client/
```

- Found an email `If you have an interest in being a part of the movement send me your CV at JD@anthem.com`

- Found a login page at http://10.10.71.4/umbraco/#/login

- When trying to login as `JD@anthem.com`. We get an error

- Found this text in the login page source code `<p>For full functionality of Umbraco CMS it is necessary to enable JavaScript.</p>`

- Used the hint, in robots.txt `UmbracoIsTheBest!` thie can be a potential password. Tried and not working.

- Ran a feroxbuster command found this in the source code of `view-source:http://10.10.71.4/search`

```
      ~  feroxbuster -u http://10.10.71.4 -w /usr/share/wordlists/medium.txt -x html,txt -n
```

```
<input type="text" name="term" placeholder="Search... THM{G!T_G00D}" />
```

- This `http://anthem.com/authors/jane-doe/` returns a 500 error

- Used writeup for this, when we copy past the poem on  google, we get the author name `Solomon Grundy`. This can be the site admin

- Now as per the hint admin email ID follows the above pattern `JD@anthem.com` now for `Solomon Grundy`, the mail will be `SG@anthem.com`

- W are able to login at http://10.10.71.4/umbraco/#/login with 
G@anthem.com:UmbracoIsTheBest!

- (Writeup) We can also find another flag in source code of `we-are-hiring` blog post and also in the other blog post
```
<meta content="THM{L0L_WH0_US3S_M3T4}" property="og:description" />

<meta content="THM{AN0TH3R_M3TA}"
```

### Umbraco

- Found the version details in help section `Umbraco version 7.15.4`
- Umbraco exploits does not work

### RDP

- Use writeup for this 
- Accessing the machine RDP using 
```
      ~  xfreerdp /v:10.10.179.160 /u:SG /p:UmbracoIsTheBest!
```


- Found the user flag on desktop
```
THM{N00T_NO0T}
```

- Now tried to get a reverse shell using msfvenom paylaod and also tried to get the winpeas file on the target machine but windows defender deleted it

- (W) we find a hidden directory in the `C:/backup` . f we try to open, we get permission denied.
we have to add our user group to the file’s property and grant all the permission over the file.

![Add User group](https://devshmsec.github.io/assets/images/tryhackme/anthem/add_user_group.png)
we get the admin password `ChangeMeBaby1MoreTime`

- We find the admin flag in admin's Desktop folder `THM{Y0U_4R3_1337}`

---

- Found this another method in a writeup to get all the flags together using wget

```
root@kali:~/thm/anthem# wget --recursive http://10.10.83.110

root@kali:~/thm/anthem# cd 10.10.83.110/

root@kali:~/thm/anthem/10.10.83.110# grep -R 'THM' .
```