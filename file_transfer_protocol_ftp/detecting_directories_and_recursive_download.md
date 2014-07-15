## Detecting Directories and Recursive Download

If you cannot guarantee what information an FTP server might choose to return from its `dir()`
command, how are you going to tell directories from normal files—an essential step to downloading
entire trees of files from the server?
The answer, shown in `recursedl.py`, is to simply try a `cwd()` into every name that `nlst()` returns and,
if you succeed, conclude that the entity is a directory. This sample program does not do any actual
downloading; instead, to keep things simple, it simply prints
out the directories it visits to the screen.
```python

import os, sys
from ftplib import FTP, error_perm

def walk_dir(f, dirpath):
    original_dir = f.pwd()
    try:
        f.cwd(dirpath)
    except error_perm:
        return  # ignore non-directores and ones we cannot enter
    print dirpath
    names = f.nlst()
    for name in names:
        walk_dir(f, dirpath + '/' + name)
    f.cwd(original_dir)  # return to cwd of our caller

f = FTP('ftp.kernel.org')
f.login()
walk_dir(f, '/pub/linux/kernel/Historic/old-versions')
f.quit()
```
This sample program will run a bit slow—there are, it turns out, quite a few files in the old-versions
directory on the Linux Kernel Archive— but within a few dozen seconds, you should see the resulting
directory tree displayed on the screen:

```
root@erlerobot:~/Python_files# python recursedl.py
/pub/linux/kernel/Historic/old-versions
/pub/linux/kernel/Historic/old-versions/impure
/pub/linux/kernel/Historic/old-versions/old
/pub/linux/kernel/Historic/old-versions/old/corrupt
/pub/linux/kernel/Historic/old-versions/tytso
```
