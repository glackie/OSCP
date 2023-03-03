A helpful exercise to complete when trying to find authentication vulnerabilities is creating a list of valid usernames, which we'll use later in other tasks.

  

Website error messages are great resources for collating this information to build our list of valid usernames. 

We have a form to create a new user account if we go to the Acme IT Support website ([http://10.10.138.116/customers/signup](http://10.10.138.116/customers/signup)) signup page.


If you try entering the username **admin** and fill in the other form fields with fake information, you'll see we get the error **An account with this username already exists**. 

We can use the existence of this error message to produce a list of valid usernames already signed up on the system by using the ffuf tool below. The ffuf tool uses a list of commonly used usernames to check against for any matches.


           `user@tryhackme$ ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.138.116/customers/signup -mr "username already exists"`


In the above example, the `-w` argument selects the file's location on the computer that contains the list of usernames that we're going to check exists. 

The `-X` argument specifies the request method, this will be a GET request by default, but it is a POST request in our example.

The `-d` argument specifies the data that we are going to send. In our example, we have the fields username, email, password and cpassword. 

We've set the value of the username to **FUZZ**. In the ffuf tool, the FUZZ keyword signifies where the contents from our wordlist will be inserted in the request. 

The `-H` argument is used for adding additional headers to the request. In this instance, we're setting the `Content-Type` to the webserver knows we are sending form data.

The `-u` argument specifies the URL we are making the request to, and finally,

the `-mr` argument is the text on the page we are looking for to validate we've found a valid username.

The ffuf tool and wordlist come pre-installed on the **AttackBox** or can be installed locally by downloading it from [https://github.com/ffuf/ffuf](https://github.com/ffuf/ffuf).  

