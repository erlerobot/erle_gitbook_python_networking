## Obtaining Mailbox Information

The preceding example showed you `stat()`, which returns the number of messages in the mailbox and
their total size. Another useful POP command is `list()`, which returns more detailed information about
each message.
The most interesting part is the message number, which is required to retrieve messages later. Note
that there may be gaps in message numbers: a mailbox may, for example, contain message numbers 1, 2,
5, 6, and 9. Also, the number assigned to a particular message may be different on each connection you
make to the POP server.
`mailbox.py` shows how to use the `list()` command to display information about each message.
```python

import getpass, poplib, sys

if len(sys.argv) != 3:
    print 'usage: %s hostname user' % sys.argv[0]
    exit(2)

hostname, user = sys.argv[1:]
passwd = getpass.getpass()

p = poplib.POP3_SSL(hostname)
try:
    p.user(user)
    p.pass_(passwd)
except poplib.error_proto, e:
    print "Login failed:", e
else:
    response, listings, octet_count = p.list()
    for listing in listings:
        number, size = listing.split()
        print "Message %s has %s bytes" % (number, size)
finally:
    p.quit()
    ```
The `list()` function returns a tuple containing three items; you should generally pay attention to
the second item. Here is its raw output for one of my POP mailboxes at the moment, which has three
messages in it:
```
('+OK 3 messages (5675 bytes)', ['1 2395', '2 1626',
'3 1654'], 24)
```
The three strings inside the second item give the message number and size for each of the three
messages in my in-box.
