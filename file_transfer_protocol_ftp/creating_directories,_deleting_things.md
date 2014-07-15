## Creating Directories, Deleting Things

Finally, FTP supports file deletion, and supports both the creation and deletion of directories. These
more obscure calls are all described in the `ftplib` documentation:
- `delete(filename)` will delete a file from the server.


- `mkd(dirname)` attempts to create a new directory.


- `rmd(dirname)` will delete a directory; note that most systems require the directory
to be empty first.


- `rename(oldname, newname)` works, essentially, like the Unix command mv: if both
names are in the same directory, the file is essentially re-named; but if the
destination specifies a name in a different directory, then the file is actually
moved.

######Doing FTP Securely

To use TLS, create your FTP connection with the FTP_TLS class instead of the plain FTP class; simply
by doing this, your username and password and, in fact, the entire FTP command channel will be
protected from prying eyes. If you then additionally run the class’s `prot_p()` method (it takes no
arguments), then the FTP data connection will be protected as well. Should you for some reason want to
return to using an un-encrypted data connection during the session, there is a `prot_c()` method that
returns the data stream to normal. Again, your commands will continue to be protected as long as you
are using the FTP_TLS class.

Check the Python Standard Library documentation for more details (they include a small code
sample) if you wind up needing this extension to FTP:
http://docs.python.org/library/ftplib.html#ftplib.FTP_TLS
