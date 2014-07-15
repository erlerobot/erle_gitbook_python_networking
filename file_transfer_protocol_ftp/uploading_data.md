## Uploading Data

File data can also be uploaded through FTP. As with downloading, there are two basic functions for
uploading: `storbinary()` and `storlines()`. Both take a command to run, and a file-like object to
transmit. The `storbinary()` function will call the `read()` method repeatedly on that object until its
content is exhausted, while `storlines()`, by contrast, calls the `readline()` method.
Unlike the corresponding download functions, these methods do not require you to provide a
callable function of your own. (But you could, of course, pass a file-like object of your own crafting
whose `read()` or `readline()` method computes the outgoing data as the transmission proceeds.
`binaryul.py` shows how to upload a file in binary mode.
```python

from ftplib import FTP
import sys, getpass, os.path

if len(sys.argv) != 5:
    print "usage: %s <host> <username> <localfile> <remotedir>" % (
        sys.argv[0])
    exit(2)

host, username, localfile, remotedir = sys.argv[1:]
password = getpass.getpass(
    "Enter password for %s on %s: " % (username, host))

f = FTP(host)
f.login(username, password)
f.cwd(remotedir)

fd = open(localfile, 'rb')
f.storbinary('STOR %s' % os.path.basename(localfile), fd)
fd.close()

f.quit()
```
This program looks quite similar to our earlier efforts. Since most anonymous FTP sites do not
permit file uploading, you will have to find a server somewhere to test it against; I simply installed the
old, venerable ftpd on my laptop for a few minutes and ran the test like this:
```
root@erlerobot:~/Python_files# python binaryul.py localhost brandon test.txt /tmp
```
You can modify this program to upload a file in ASCII mode by simply changing `storbinary()` to
`storlines()`.
