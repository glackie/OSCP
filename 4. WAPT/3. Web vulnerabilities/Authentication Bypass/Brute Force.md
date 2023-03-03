
Using the valid_usernames.txt file we generated in the previous task(username enumeration), we can now use this to attempt a brute force attack on the login page ([http://10.10.138.116/customers/login](http://10.10.138.116/customers/login)).

**Note: If you created your valid_usernames file by piping the output from ffuf directly you may have difficulty with this task. Clean your data, or copy just the names into a new file.**

A brute force attack is an automated process that tries a list of commonly used passwords against either a single username or, like in our case, a list of usernames.

When running this command, make sure the terminal is in the same directory as the valid_usernames.txt file.

           `user@tryhackme$ ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.10.138.116/customers/login -fc 200`

This ffuf command is a little different to the previous one in Task 2.

Previously we used the **FUZZ** keyword to select where in the request the data from the wordlists would be inserted, but because we're using multiple wordlists, we have to specify our own FUZZ keyword. 

In this instance, we've chosen `W1` for our list of valid usernames and `W2` for the list of passwords we will try. 

The multiple wordlists are again specified with the `-w` argument but separated with a comma.  

For a positive match, we're using the `-fc` argument to check for an HTTP status code other than 200.