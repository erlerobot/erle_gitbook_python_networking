##  IMAPClient

Fortunately, a popular and battle-tested IMAP library for Python does exist, and is available for easy installation
from the Python Package Index. The [IMAPClient package](https://pypi.python.org/pypi/IMAPClient/0.11) is written by a friendly Python programmer named
Menno Smits, and in fact uses the Standard Library  Once installed,
you can use the python interpreter in the virtual environment to run the program shown in `open_imap.py`.
```python
import getpass, sys
from imapclient import IMAPClient

try:
    hostname, username = sys.argv[1:]
except ValueError:
    print 'usage: %s hostname username' % sys.argv[0]
    sys.exit(2)

c = IMAPClient(hostname, ssl=True)
try:
    c.login(username, getpass.getpass())
except c.Error, e:
    print 'Could not log in:', e
    sys.exit(1)

print 'Capabilities:', c.capabilities()
print 'Listing mailboxes:'
data = c.list_folders()
for flags, delimiter, folder_name in data:
    print '  %-30s%s %s' % (' '.join(flags), delimiter, folder_name)
c.logout()
```
You can see immediately from the code that more details of the protocol exchange are now being
handled on our behalf. For example, we no longer get a status code back that we have to check every
time we run a command; instead, the library is doing that check for us and will raise an exception to stop
us in our tracks if anything goes wrong.
Second, you can see that each result from the LIST command—which in this library is offered as the
`list_folders()` method instead of the `list()` method offered by imaplib—has already been parsed into
Python data types for us. Each line of data comes back as a tuple giving us the folder flags, folder name
delimiter, and folder name, and the flags themselves are a sequence of strings.
Take a look at the code below,for what the output of this second script looks like:
```
Capabilities: ('IMAP4REV1', 'UNSELECT', 'IDLE', 'NAMESPACE', 'QUOTA', 'XLIST', 'CHILDREN',
'XYZZY', 'SASL-IR', 'AUTH=XOAUTH')
Listing mailboxes:
\HasNoChildren / INBOX
\HasNoChildren / Personal
\HasNoChildren / Receipts
\HasNoChildren / Travel
\HasNoChildren / Work
\Noselect \HasChildren / [Gmail]
\HasChildren \HasNoChildren / [Gmail]/All Mail
\HasNoChildren / [Gmail]/Drafts
\HasChildren \HasNoChildren / [Gmail]/Sent Mail
\HasNoChildren / [Gmail]/Spam
\HasNoChildren / [Gmail]/Starred
\HasChildren \HasNoChildren / [Gmail]/Trash
```
The standard flags listed for each folder may be zero or more of the following:

- \Noinferiors: This means that the folder does not contain any sub-folders and
that it is not possible for it to contain sub-folders in the future. Your IMAP client
will receive an error if it tries to create a sub-folder under this folder.


-  \Noselect: This means that it is not possible to run `select_folder()` on this
folder—that is, this folder does not and cannot contain any messages. (Perhaps it
exists just to allow sub-folders beneath it, as one possibility.)


- \Marked: This means that the server considers this box to be interesting in some
way; generally, this indicates that new messages have been delivered since the last
time the folder was selected. However, the absence of \Marked does not guarantee
that the folder does not contain new messages; some servers simply do not
implement \Marked at all.


- \Unmarked: This guarantees that the folder doesn’t contain new messages.
