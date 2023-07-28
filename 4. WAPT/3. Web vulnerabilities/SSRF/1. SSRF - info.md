**Room Brief**

In this room, you'll learn what an SSRF is, what kind of impact they can have, you'll view some example SSRF attacks, how you can discover SSRF vulnerabilities, how to circumvent input rules and then we have a practice for you against with to try your newfound skills.

  

**What is an SSRF?**

SSRF stands for Server-Side Request Forgery. It's a vulnerability that allows a malicious user to cause the webserver to make an additional or edited HTTP request to the resource of the attacker's choosing.

  

**Types of SSRF**

There are two types of SSRF vulnerability; the first is a regular SSRF where data is returned to the attacker's screen. 

The second is a Blind SSRF vulnerability where an SSRF occurs, but no information is returned to the attacker's screen.

## **What's the impact?**

A successful SSRF attack can result in any of the following: 

-   Access to unauthorised areas.
-   Access to customer/organisational data.
-   Ability to Scale to internal networks.
-   Reveal authentication tokens/credentials.