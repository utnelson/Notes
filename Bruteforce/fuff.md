This ffuf command is a little different to the previous one in Task 2. Previously we used the FUZZ keyword to select where in the request the data from the wordlists would be inserted, but because we're using multiple wordlists, we have to specify our own FUZZ keyword. In this instance, we've chosen W1 for our list of valid usernames and W2 for the list of passwords we will try. The multiple wordlists are again specified with the -w argument but separated with a comma.  For a positive match, we're using the -fc argument to check for an HTTP status code other than 200.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/
10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H 
"Content-Type: application/x-www-form-urlencoded" -u http://10.10.102.18/customers/login -fc 200
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
