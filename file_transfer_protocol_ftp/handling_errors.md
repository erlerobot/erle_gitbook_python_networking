## Handling Errors

Like most Python modules, `ftplib` will raise an exception when an error occurs. It defines several
exceptions of its own, and it can also raise socket.error and IOError. As a convenience, it offers a tuple,
named `ftplib.all_errors`, that lists all of the exceptions that can possibly be raised by ftplib. This is
often a useful shortcut for writing a try…except clause.

One of the problems with the basic `retrbinary()` function is that, in order to use it easily, you will
usually wind up opening the file on the local end before beginning the transfer on the remote side. If
your command aimed at the remote side retorts that the file does not exist, or if the RETR command
otherwise fails, then you will have to close and delete the local file you have just created (or else wind up
littering the filesystem with zero-length files).

With the `ntransfercmd()` method, by contrast, you can check for a problem prior to opening a local
file. `nlst.py` already follows these guidelines: if ntransfercmd() fails, the exception will cause the
program to terminate before the local file is opened.
Scanning DirectoriesFTP provides two ways to discover information about server files and directories.
These are implemented in ftplib as the `nlst()` and `dir()` methods.

- The `nlst()` method returns a list of entries in a given directory—all of the files and directories inside.
However, the bare names are all that is returned. There is no other information about which particular
entries are files or are directories, on the sizes of the files present, or anything else.


- The more powerful `dir()` function returns a directory listing from the remote. This listing is in a
system-defined format, but typically contains a file name, size, modification date, and file type. On UNIX
servers, it is typically the output of one of these two shell commands:
```
root@erlerobot:~# ls -l
root@erlerobot:~# ls -la
```

`nlst.py` shows an example of using nlst() to get directory information.

```python

from ftplib import FTP

f = FTP('ftp.ibiblio.org')
f.login()
f.cwd('/pub/academic/astronomy/')
entries = f.nlst()
entries.sort()
print len(entries), "entries:"
for entry in entries:
    print entry
f.quit()
```

`nlst.py` shows an example of using nlst() to get directory information. When you run this
program, you will see output like this:
```
root@erlerobot:~/Python_files# python nlst.py
13 entries:
INDEX
README
ephem_4.28.tar.Z
hawaii_scope
incoming
jupitor-moons.shar.Z
lunar.c.Z
lunisolar.shar.Z
moon.shar.Z
planetary
sat-track.tar.Z
stars.tar.Z
xephem.tar.Z
```

If you were to use an FTP client to manually log on to the server, you would see the same files listed.
Notice that the file names are in a convenient format for automated processing—a bare list of file
names—but that there is no extra information. The result will be different when we try another file
listing command in `dir.py`:

```python
from ftplib import FTP

f = FTP('ftp.ibiblio.org')
f.login()
f.cwd('/pub/academic/astronomy/')
entries = []
f.dir(entries.append)
print "%d entries:" % len(entries)
for entry in entries:
    print entry
f.quit()
```

Notice that the filenames are in a convenient format for automated processing — a bare list of
filenames — but that is no extra information. Contrast the bare list of file names we saw earlier with the
output from `dir.py`, which uses `dir()`:

```
root@erlerobot:~/Python_files# python dir.py
13 entries:
-rw-r--r-- 1 (?) » (?) » » 750 Feb 14 1994 INDEX
-rw-r--r-- 1 root » bin » » 135 Feb 11 1999 README
-rw-r--r-- 1 (?) » (?) » 341303 Oct 2 1992 ephem_4.28.tar.Z
drwxr-xr-x 2 (?) » (?) » » 4096 Feb 11 1999 hawaii_scope
drwxr-xr-x 2 (?) » (?) » » 4096 Feb 11 1999 incoming
-rw-r--r-- 1 (?) » (?) » » 5983 Oct 2 1992 jupitor-moons.shar.Z
-rw-r--r-- 1 (?) » (?) » » 1751 Oct 2 1992 lunar.c.Z
-rw-r--r-- 1 (?) » (?) » » 8078 Oct 2 1992 lunisolar.shar.Z
-rw-r--r-- 1 (?) » (?) » » 64209 Oct 2 1992 moon.shar.Z
drwxr-xr-x 2 (?) » (?) » » 4096 Jan 6 1993 planetary
-rw-r--r-- 1 (?) » (?) » 129969 Oct 2 1992 sat-track.tar.Z
-rw-r--r-- 1 (?) » (?) » » 16504 Oct 2 1992 stars.tar.Z
-rw-r--r-- 1 (?) » (?) » 410650 Oct 2 1992 xephem.tar.Z
```
The `dir()` method takes a function that it calls for each line, delivering the directory listing in pieces
just like `retrlines()` delivers the contents of particular files. Here, we simply supply the `append()`
method of our plain old Python entries list.

