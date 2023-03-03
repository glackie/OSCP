
## **Where are they located?**

The vulnerable endpoint you're targeting may not always be something you see in the address bar. 


It could be content your browser loads in via an AJAX request or something that you find referenced in a JavaScript file. 


Sometimes endpoints could have an unreferenced parameter that may have been of some use during development and got pushed to production. 

For example, you may notice a call to **/user/details** displaying your user information (authenticated through your session). But through an attack known as parameter mining, you discover a parameter called **user_id** that you can use to display other users' information, for example, **/user/details?user_id=123**.