## Sub-domains

A sub-domain is any website underlying another domain. For example, `https://photos.google.com` is the `photos` sub-domain of `google.com`.

In this case, we are simply checking different websites to see if they exist by checking if they have a public DNS record that would redirect us to a working server IP. 

So, let's run a scan and see if we get any hits. Before we can start our scan, we need two things:

-   A `wordlist`
-   A `target`

Luckily for us, in the `SecLists` repo, there is a specific section for sub-domain wordlists, consisting of common words usually used for sub-domains. 
We can find it in `/opt/useful/SecLists/Discovery/DNS/`. 

In our case, we would be using a shorter wordlist, which is `subdomains-top1million-5000.txt`. If we want to extend our scan, we can pick a larger list.

As for our target, we will use `hackthebox.eu` as our target and run our scan on it. Let us use `ffuf` and place the `FUZZ` keyword in the place of sub-domains, and see if we get any hits:

```shell-session
ack@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.hackthebox.eu/
```

We see that we do get a few hits back. We can verify that these are actual sub-domains by visiting one of them:

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_htb_help.jpg)

We see that indeed these are working sub-domains. Now, we can try running the same thing on `academy.htb` and see if we get any hits back:

```shell-session
glack@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.academy.htb/
```

We see that we do not get any hits back. Does this mean that there are no sub-domain under `academy.htb`? - No.

This means that there are no `public` sub-domains under `academy.htb`, as it does not have a public DNS record, as previously mentioned. 

Even though we did add `academy.htb` to our `/etc/hosts` file, we only added the main domain, so when `ffuf` is looking for other sub-domains, it will not find them in `/etc/hosts`, and will ask the public DNS, which obviously will not have them.