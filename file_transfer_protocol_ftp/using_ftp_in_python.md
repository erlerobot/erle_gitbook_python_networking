## Using FTP in Python

The Python module [ftplib](https://docs.python.org/2/library/ftplib.html?highlight=ftplib#ftplib) is the primary interface to FTP for Python programmers. It handles the
details of establishing the various connections for you, and provides convenient ways to automate
common commands. You can use this to write Python programs that perform a variety of automated FTP jobs, such as mirroring other ftp servers. It is also used by the module urllib to handle URLs that use FTP. For more information on FTP (File Transfer Protocol), see Internet [RFC 959](http://tools.ietf.org/html/rfc959.html).

`connect.py` shows a very basic ftplib example. The program connects to a remote server, displays
the welcome message, and prints the current working directory.
```python

from ftplib import FTP

f = FTP('ftp.ibiblio.org')
print "Welcome:", f.getwelcome()
f.login()

print "Current working directory:", f.pwd()
f.quit()
````
Recall that an FTP session can visit different directories, just like a shell prompt can move between
locations with cd. Here, the `pwd()` function returns the current working directory on the remote site of
the connection. Finally, the `quit()` function logs out and closes the connection.
Here is what the program outputs when run:
```
root@erlerobot:~/Python_files# python connect.py
Welcome: 220 ProFTPD Server
Current working directory: /
```
