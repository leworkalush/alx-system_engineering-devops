504 Error while accessing a given URL
A 504 error is an HTTP status code that means that one server didn’t receive a timely response from another server that 
it was accessing while attempting to load the web page or fill another request by the browser1. 
It is a server-side error which means the problem exists with the website you’re trying to access, and not with your computer2.
In your case, it seems like the server access went down resulting in 504 error for anyone trying to access a website. 
The background on the server being based on a LAMP stack3. The timeline of events that followed are as follows:
00:00 PST - 500 error or anyone trying to access the website
00:05 PST - Ensuring Apache and MySQL are up and running.
00:10 PST - The website was not loading properly which on background check revealed that the server was working properly as well as the database.
00:12 PST - After quick restart to Apache server returned a status of 200 and OK while trying to curl the website.
00:18 PST - Reviewing error logs to check where the error might be coming from.
00:25 PST - Check /var/log to see that the Apache server was being prematurely shut down. The error log for PHP were nowhere to be found.
00:30 PST - Checking php.ini settings revealed all error logging had been turned off. Turning the error logging on.
00:32 PST - Restarting apache server and going to the error logs to check what is being logged into the php error logs.
00:36 PST - Reviewing error logs for php revealed a mistyped file name which was resulting in incorrect loading and premature closing of apache.
00:38 PST - Fixing file name and restarting Apache server.
00:40 PST - Server is now running normal and the website is loading properly.
Based on the information you provided, it seems that the issue was connected with a wrong file name being referred to in the wp-settings.php file.
The error was raised when trying to curl the server, wherein the server responded with 500 error. 
By checking the error logs it was found that no error log file was being created for the php errors and reading the default error log for
apache did not result in much information regarding the premature closing of the server. Once understood that the errors for php logs were not being directed
anywhere the engineer chose to review the error log setting for the php in the php.ini file and found that all error logging was turned off. 
Once turned on, the error logging the apache server was restarted to check if any errors were being registered in the log. As suspected, 
the php log showed that a file with a .phpp extension was not found in the wp-settings.php file. This was clearly a misspelled error that resulted in the error to 
site access. As this was one server that the error was found in, this error might have been replicated in other servers as well. An easy fix by changing the file 
extension by puppet would result in the fix being made to other servers as well. A quick deployment of the puppet code replaced all misspelled file extensions with
the right one and restarting of the server resulted in properly loading of the site and server.
Correctional and Preventative Actions
Error logging should be enabled on all servers and websites to make it simple to find faults when something goes wrong.
Before deploying on a multi-server setup, all servers and sites should be tested locally.
This will allow issues to be fixed before coming live and reduce the amount of time needed to fix a downed site.
