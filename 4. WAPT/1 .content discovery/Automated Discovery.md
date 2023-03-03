Automated discovery is the process of using tools to discover content rather than doing it manually.

**Automation Tools**

Although there are many different content discovery tools available, all with their features and flaws, we're going to cover three which are preinstalled on our attack box, 

	ffuf, dirb and gobuster.


Examples: 

**Using ffuf:**

ffuf

```shell-session
user@machine$ ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -u http://10.10.129.106/FUZZ
```

**Using dirb:**

dirb

```shell-session
user@machine$ dirb http://10.10.129.106/ /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
```

**Using Gobuster:**

gobuster

```shell-session
user@machine$ gobuster dir --url http://10.10.129.106/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
```