Potential SSRF vulnerabilities can be spotted in web applications in many different ways. Here is an example of four common places to look:

**When a full URL is used in a parameter in the address bar:**

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5efe36fb68daf465530ca761/room-content/956e1914b116cbc9e564e3bb3d9ab50a.png)  

**A hidden field in a form:**

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5efe36fb68daf465530ca761/room-content/237696fc8e405d25d4fc7bbcc67919f0.png)  

**A partial URL such as just the hostname:**

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5efe36fb68daf465530ca761/room-content/f3c387849e91a4f15a7b59ff7324be75.png)

  

**Or perhaps only the path of the URL:**

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5efe36fb68daf465530ca761/room-content/3fd583950617f7a3713a107fcb4cfa49.png)

Some of these examples are easier to exploit than others, and this is where a lot of trial and error will be required to find a working payload.

If working with a blind SSRF where no output is reflected back to you, you'll need to use an external HTTP logging tool to monitor requests such as requestbin.com, your own HTTP server or Burp Suite's Collaborator client.