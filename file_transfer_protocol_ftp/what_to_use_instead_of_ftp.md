## What to Use Instead of FTP

Today, there are better alternatives than the FTP protocol for pretty much anything you could want to do
with it.

The biggest problem with the protocol is its lack of security: not only files, but usernames and
passwords are sent completely in the clear and can be viewed by anyone observing network traffic.

A second issue is that an FTP user tends to make a connection, choose a working directory, and do
several operations all over the same network connection. Modern Internet services, with millions of
users, prefer protocols like HTTP that consist of short, completely self-contained
requests, instead of long-running FTP connections that require the server to remember things like a
current working directory.

A final big issue is filesystem security. The early FTP servers, instead of showing users just a sliver of
the host filesystem that the owner wanted exposed, tended to simply expose the entire filesystem, letting
users cd to / and snoop around to see how the system was configured.

######So what are the alternatives?

- For file download, HTTP is the standard protocol on today’s Internet,
protected with SSL when necessary for security. Instead of exposing systemspecific
file name conventions like FTP, HTTP supports system-independent
URLs.


- Anonymous upload is a bit less standard, but the general tendency is to use a form
on a web page that instructs the browser to use an HTTP POST operation to
transmit the file that the user selects.


- File synchronization has improved immeasurably since the days when a recursive
FTP file copy was the only common way to get files to another computer. Instead
of wastefully copying every file, modern commands like rsync or rdist efficiently
compare files at both ends of the connection and copy only the ones that are new
or have changed.


- Full filesystem access is actually the one area where FTP can still commonly be
found on today’s Internet: thousands of cut-rate ISPs continue to support FTP,
despite its insecurity, as the means by which users copy their media and
(typically) PHP source code into their web account. A much better alternative
today is for service providers to support SFTP instead.
