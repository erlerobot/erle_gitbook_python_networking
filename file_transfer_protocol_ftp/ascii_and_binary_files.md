## ASCII and Binary Files

When making an FTP transfer, you have to decide whether you want the file treated as a monolithic
block of binary data, or whether you want it parsed as a text file so that your local machine can paste its
lines back together using whatever end-of-line character is native to your platform.
A file transferred in so-called “ASCII mode” is delivered one line at a time, so that you can glue the
lines back together on the local machine using its own line-ending convention. Take a look at
`asciidl.py` for a Python program that downloads a well-known text file and saves it in your local directory.
```

import os
from ftplib import FTP

if os.path.exists('README'):
    raise IOError('refusing to overwrite your README file')

def writeline(data):
    fd.write(data)
    fd.write(os.linesep)

f = FTP('ftp.kernel.org')
f.login()
f.cwd('/pub/linux/kernel')

fd = open('README', 'w')
f.retrlines('RETR README', writeline)
fd.close()

f.quit()
```

In the example, the `cwd()` function selects a new working directory on the remote system. Then the
`retrlines()` function begins the transfer. Its first parameter specifies a command to run on the remote
system, usually RETR, followed by a file name. Its second parameter is a function that is called, over and
over again, as each line of the text file is retrieved; if omitted, the data is simply printed to standard
output. The lines are passed with the end-of-line character stripped, so the homemade `writeline()`
function simply appends your system’s standard line ending to each line as it is written out.
Try running this program; there should be a file in your current directory named README after the
program is done.
Basic binary file transfers work in much the same way as text-file transfers; `binarydl.py`shows an
example.
```python

import os
from ftplib import FTP

if os.path.exists('patch8.gz'):
    raise IOError('refusing to overwrite your patch8.gz file')

f = FTP('ftp.kernel.org')
f.login()
f.cwd('/pub/linux/kernel/v1.0')

fd = open('patch8.gz', 'wb')
f.retrbinary('RETR patch8.gz', fd.write)
fd.close()

f.quit()
```
When run, it deposits a file named patch8.gz in your current working directory. The `retrbinary()`
function simply passes blocks of data to the specified function. This is convenient, since a file object’s
`write()` function expects just such data—so in this case, no custom function is necessary.

