## Advanced Binary Downloading

The `ftplib` module provides a second function that can be used for binary downloading:
`ntransfercmd()`. This command provides a lower-level interface, but can be useful if you want to know a
little bit more about what’s going on during the download.
In particular, this more advanced command lets you keep track of the number of bytes transferred,
and you can use that information to display status updates for the user. `advbinarydl.py` shows a sample
program that uses `ntransfercmd()`.

```python
import os, sys
from ftplib import FTP

if os.path.exists('linux-1.0.tar.gz'):
    raise IOError('refusing to overwrite your linux-1.0.tar.gz file')

f = FTP('ftp.kernel.org')
f.login()

f.cwd('/pub/linux/kernel/v1.0')
f.voidcmd("TYPE I")

datasock, size = f.ntransfercmd("RETR linux-1.0.tar.gz")
bytes_so_far = 0
fd = open('linux-1.0.tar.gz', 'wb')

while 1:
    buf = datasock.recv(2048)
    if not buf:
        break
    fd.write(buf)
    bytes_so_far += len(buf)
    print "\rReceived", bytes_so_far,
    if size:
        print "of %d total bytes (%.1f%%)" % (
            size, 100 * bytes_so_far / float(size)),
    else:
        print "bytes",
    sys.stdout.flush()

print
fd.close()
datasock.close()
f.voidresp()
f.quit()
```
There are a few new things to note here. First comes the call to `voidcmd()`. This passes an FTP
command directly to the server, checks for an error, but returns nothing. In this case, the raw command
is TYPE I. That sets the transfer mode to “image,” which is how FTP refers internally to binary files. In the
previous example, `retrbinary()` automatically ran this command behind the scenes, but the lower-level
`ntransfercmd()` does not.
Next, note that `ntransfercmd()` returns a tuple consisting of a data socket and an estimated size.
Always bear in mind that the size is merely an estimate, and should not be considered authoritative; the
file may end sooner, or it might go on much longer, than this value. Also, if a size estimate from the FTP
server is simply not available, then the estimated size returned will be None.

After receiving the data, it is important to close the data socket and call `voidresp()`, which reads the
command response code from the server, raising an exception if there was any error during
transmission. Even if you do not care about detecting errors, failing to call `voidresp()` will make future
commands likely to fail because the server’s output socket will be blocked waiting for you to read the
results.
Here is an example of running this program:
```
root@erlerobot:~/Python_files# python advbinarydl.py
Received 1259161 of 1259161 bytes (100.0%)
```
