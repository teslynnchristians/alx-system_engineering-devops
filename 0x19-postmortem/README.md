Postmortem Report
504 Error while accessing a given URL

Summary

On October 12th , 2017 at about 14:00 the server access went down resulting in 504 error for anyone trying to access the website. Background on the server being based on a LAMP stack.

Timeline
14:00 PST - 500 error for everyone who tried to access the website.
14:05 PST - Ensuring Apache and MySQL was up and running.
14:10 PST - The website was not loading properly which on the background check showed that the server and database was working properly.
14:12 PST - After a quick restart the Apache server returned a status of 200 and OK while trying to curl the website.
14:18 PST - Reviewing error logs to check where the error might be coming from.
14:25 PST - Check /var/log to see that the Apache server was being prematurely shut down. The error logs for PHP were nowhere to be found.
14:30 PST - Checking php.ini settings revealed all error logging had been turned off. Turning the error logging on.
14:32 PST - Restarting apache server and going to the error logs to check what is being logged into the php error logs.
14:36 PST - Reviewing error logs for php revealed a mistyped file name which was resulting in incorrect loading and premature closing of apache.
14:38 PST - Fixing file name and restarting Apache server.
14:40 PST - Server is now running normally and the website is loading properly.
Root Cause and Resolution
The issue was connected with a wrong file name being referred to in the wp-settings.php file. The error was raised when trying to curl the server, wherein the server responded with 500 errors. By checking the error logs it was found that no error log file was being created for the php errors and reading the default error log for apache did not result in much information regarding the premature closing of the server. Once understood that the errors for php logs were not being directed anywhere the engineer chose to review the error log setting for the php in the php.ini file and found that all error logging was turned off. Once turned on, the error logging the apache server was restarted to check if any errors were being registered in the log. As suspected, the php log showed that a file with a .php extension was not found in the wp-settings.php file. This was clearly a misspelled error that resulted in the error to site access. As this was one server that the error was found in, this error might have been replicated in other servers as well. An easy fix by changing the file extension by puppet would result in the fix being made to other servers as well. A quick deployment of the puppet code replaced all misspelled file extensions with the right one and restarting of the server resulted in proper loading of the site and server.
 
Corrective and Preventive Measures
All servers and sites should have error logging turned on to easily identify errors if anything goes wrong.
All servers and sites should be tested locally before deploying on a multi-server setup; this will result in correcting errors before going live resulting in less fixing time if the site goes down.
