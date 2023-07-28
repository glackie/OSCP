Firstly you'll need to log in. To do this, click on the customer's section and create an account. Once logged in, click on the **Your Account** tab. 

  

The **Your Account** section gives you the ability to change your information such as username, email address and password. You'll notice the username and email fields pre-filled in with your information.  

  

We'll start by investigating how this information gets pre-filled. If you open your browser developer tools, select the network tab and then refresh the page, you'll see a call to an endpoint with the path /api/v1/customer?id=`{user_id}`.

  

This page returns in JSON format your user id, username and email address. We can see from the path that the user information shown is taken from the query string's id parameter (see below image).

>Upon opening the inspect tab in mozilla I saw a packet sent by browser 

>XHRGEThttps://10-10-230-79.p.thmlabs.com/api/v1/customer?id=16

>Above packet was sent by the browser

>I selected the packet

>Went to the right side where all the request headers and response tab was present

>headers option me headers likha hai uske baju me ek resent ka option the, i selected that

>select karneke bad left side me packet ke andar chanegs karne ka option aya

>yaha pe me {id} parameter ka value change karke packet wapis send kiya anad i got the result of that id and a new packet was sent

>us new packet ke response me i got my flag

