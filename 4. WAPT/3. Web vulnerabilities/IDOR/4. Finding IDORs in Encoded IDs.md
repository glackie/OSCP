## **Encoded IDs**

When passing data from page to page either by post data, query strings, or cookies, web developers will often first take the raw data and encode it. 

Encoding ensures that the receiving web server will be able to understand the contents. 

Encoding changes binary data into an ASCII string commonly using the `a-z, A-Z, 0-9 and =` character for padding.

The most common encoding technique on the web is base64 encoding and can usually be pretty easy to spot. You can use websites like [https://www.base64decode.org/](https://www.base64decode.org/) to decode the string, then edit the data and re-encode it again using [https://www.base64encode.org/](https://www.base64encode.org/) and then resubmit the web request to see if there is a change in the response.  
  
See the image below as a graphical example of this process:  

  

  

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5efe36fb68daf465530ca761/room-content/5f2cbe5c4ab4a274420bc9a9afc9202d.png)