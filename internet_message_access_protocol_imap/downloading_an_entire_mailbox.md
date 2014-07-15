## Downloading an Entire Mailbox

With IMAP, the FETCH command is used to download mail, which IMAPClient exposes as its `fetch()
method.
The simplest way to fetch involves downloading all messages at once, in a single big gulp. While this
is simplest and requires the least network traffic (since you do not have to issue repeated commands and
receive multiple responses), it does mean that all of the returned messages will need to sit in memory
Download from together as your program examines them.For very large mailboxes whose messages have lots of
attachments, this is obviously not practical.

`mailbox_summary.py` downloads all of the messages from my INBOX folder into your computer’s memory in a
Python data structure, and then displays a bit of summary information about each one.
```python

import email, getpass, sys
from imapclient import IMAPClient

try:
    hostname, username, foldername = sys.argv[1:]
except ValueError:
    print 'usage: %s hostname username folder' % sys.argv[0]
    sys.exit(2)

c = IMAPClient(hostname, ssl=True)
try:
    c.login(username, getpass.getpass())
except c.Error, e:
    print 'Could not log in:', e
    sys.exit(1)

c.select_folder(foldername, readonly=True)
msgdict = c.fetch('1:*', ['BODY.PEEK[]'])
for message_id, message in msgdict.items():
    e = email.message_from_string(message['BODY[]'])
    print message_id, e['From']
    payload = e.get_payload()
    if isinstance(payload, list):
        part_content_types = [ part.get_content_type() for part in payload ]
        print '  Parts:', ' '.join(part_content_types)
    else:
        print '  ', ' '.join(payload[:60].split()), '...'

c.logout()
```
Remember that IMAP is stateful: first we use `select_folder()` to put us “inside” the given folder,
and then we can run fetch() to ask for message content.  The range '1:*' means “the first message through
the end of the mail folder”, because message IDs—whether temporary or UIDs—are always positive
integers.

Here is what it looks like to run this script:
```
root@erlerobot:~/Python_files# python  mailbox_summary.py imap.example.com brandon INBOX
Password:
2590 "Amazon.com" <order-update@amazon.com>
Dear Brandon, Portable Power Systems, Inc. shipped the follo ...
2469 Meetup Reminder <info@meetup.com>
Parts: text/plain text/html
2470 billing@linode.com
Thank you. Please note that charges will appear as "Linode.c ...
```
