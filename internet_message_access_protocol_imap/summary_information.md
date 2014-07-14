## Summary Information

 When you first select a folder, the IMAP server provides some summary information about it—about the
folder itself and also about its messages.
The summary is returned by `IMAPClient` as a dictionary. Here are the keys that most IMAP servers
will return when you run `select_folder()`:

- EXISTS: An integer giving the number of messages in the folder.

- FLAGS: A list of the flags that can be set on messages in this folder.


- RECENT: Specifies the server’s approximation of the number of messages that have
appeared in the folder since the last time an IMAP client ran `select_folder()` on
it.


- PERMANENTFLAGS: Specifies the list of custom flags that can be set on messages; this
is usually empty.


- UIDNEXT: The server’s guess about the UID that will be assigned to the next
incoming (or uploaded) message


- UIDVALIDITY: A string that can be used by clients to verify that the UID numbering
has not changed; if you come back to a folder and this is a different value than the
last time you connected, then the UID number has started over and your stored
UID values are no longer valid.


- UNSEEN: Specifies the message number of the first unseen message (one without
the \Seen flag) in the folder.

Of these flags, servers are only required to return FLAGS, EXISTS, and RECENT, though most will
include at least UIDVALIDITY as well.

 `folder_info.py`shows an example program that reads and displays the
summary information of my INBOX mail folder:
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
else:
    select_dict = c.select_folder('INBOX', readonly=True)
    for k, v in select_dict.items():
        print '%s: %r' % (k, v)
    c.logout()
    ```

 When run, this program displays results such as this:
    ```

root@erlerobot:~/Python_files# python folder_info.py imap.example.com brandon@example.com
Password:
EXISTS: 3
PERMANENTFLAGS: ('\\Answered', '\\Flagged', '\\Draft', '\\Deleted', '\\Seen', '\\*')
READ-WRITE: True
UIDNEXT: 2626
FLAGS: ('\\Answered', '\\Flagged', '\\Draft', '\\Deleted', '\\Seen')
UIDVALIDITY: 1
RECENT: 0

```

That shows that my INBOX folder contains three messages, none of which have arrived since I last
checked. If your program is interested in using UIDs that it stored during previous sessions, remember
to compare the UIDVALIDITY to a stored value from a previous session.
