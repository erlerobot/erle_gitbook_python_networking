## Downloading Messages Individually

E-mail messages can be quite large, and so can mail folders—many mail systems permit users to have
hundreds or thousands of messages, that can each be 10MB or more. That kind of mailbox can easily
exceed the RAM on the client machine if its contents are all downloaded at once, as in the previous
example.
To help network-based mail clients that do not want to keep local copies of every message, IMAP
supports several operations besides the big “fetch the whole message” command that we saw in the
previous section.
- An e-mail’s headers can be downloaded as a block of text, separately from the
message.



- Particular headers from a message can be requested and returned.


- The server can be asked to recursively explore and return an outline of the MIME
structure of a message.


- The text of particular sections of the message can be returned.

This allows IMAP clients to perform very efficient queries that download only the information they
need to display for the user, decreasing the load on the IMAP server and the network, and allowing
results to be displayed more quickly to the user.
For an example of how a simple IMAP client works, examine `simple_client.py`, which puts together a
number of ideas about browsing an IMAP account. Hopefully this provides more context than would be
possible if these features were spread out over a half-dozen shorter program listings at this point in the
chapter. You can see that the client consists of three concentric loops that each take input from the user
as he or she views the list of mail folders, then the list of messages within a particular mail folder, and
finally the sections of a specific message.

```python


import getpass, sys
from imapclient import IMAPClient

try:
    hostname, username = sys.argv[1:]
except ValueError:
    print 'usage: %s hostname username' % sys.argv[0]
    sys.exit(2)

banner = '-' * 72

c = IMAPClient(hostname, ssl=True)
try:
    c.login(username, getpass.getpass())
except c.Error, e:
    print 'Could not log in:', e
    sys.exit(1)

def display_structure(structure, parentparts=[]):
    """Attractively display a given message structure."""

    # The whole body of the message is named 'TEXT'.

    if parentparts:
        name = '.'.join(parentparts)
    else:
        print 'HEADER'
        name = 'TEXT'

    # Print this part's designation and its MIME type.

    is_multipart = isinstance(structure[0], list)
    if is_multipart:
        parttype = 'multipart/%s' % structure[1].lower()
    else:
        parttype = ('%s/%s' % structure[:2]).lower()
    print '%-9s' % name, parttype,

    # For a multipart part, print all of its subordinate parts; for
    # other parts, print their disposition (if available).

    if is_multipart:
        print
        subparts = structure[0]
        for i in range(len(subparts)):
            display_structure(subparts[i], parentparts + [ str(i + 1) ])
    else:
        if structure[6]:
            print 'size=%s' % structure[6],
        if structure[8]:
            disposition, namevalues = structure[8]
            print disposition,
            for i in range(0, len(namevalues), 2):
                print '%s=%r' % namevalues[i:i+2]
        print

def explore_message(c, uid):
    """Let the user view various parts of a given message."""

    msgdict = c.fetch(uid, ['BODYSTRUCTURE', 'FLAGS'])

    while True:
        print
        print 'Flags:',
        flaglist = msgdict[uid]['FLAGS']
        if flaglist:
            print ' '.join(flaglist)
        else:
            print 'none'
        display_structure(msgdict[uid]['BODYSTRUCTURE'])
        print
        reply = raw_input('Message %s - type a part name, or "q" to quit: '
                          % uid).strip()
        print
        if reply.lower().startswith('q'):
            break
        key = 'BODY[%s]' % reply
        try:
            msgdict2 = c.fetch(uid, [key])
        except c._imap.error:
            print 'Error - cannot fetch section %r' % reply
        else:
            content = msgdict2[uid][key]
            if content:
                print banner
                print content.strip()
                print banner
            else:
                print '(No such section)'

def explore_folder(c, name):
    """List the messages in folder `name` and let the user choose one."""

    while True:
        c.select_folder(name, readonly=True)
        msgdict = c.fetch('1:*', ['BODY.PEEK[HEADER.FIELDS (FROM SUBJECT)]',
                                  'FLAGS', 'INTERNALDATE', 'RFC822.SIZE'])
        print
        for uid in sorted(msgdict):
            items = msgdict[uid]
            print '%6d  %20s  %6d bytes  %s' % (
                uid, items['INTERNALDATE'], items['RFC822.SIZE'],
                ' '.join(items['FLAGS']))
            for i in items['BODY[HEADER.FIELDS (FROM SUBJECT)]'].splitlines():
                print ' ' * 6, i.strip()

        reply = raw_input('Folder %s - type a message UID, or "q" to quit: '
                          % name).strip()
        if reply.lower().startswith('q'):
            break
        try:
            reply = int(reply)
        except ValueError:
            print 'Please type an integer or "q" to quit'
        else:
            if reply in msgdict:
                explore_message(c, reply)

    c.close_folder()

def explore_account(c):
    """Display the folders in this IMAP account and let the user choose one."""

    while True:

        print
        folderflags = {}
        data = c.list_folders()
        for flags, delimiter, name in data:
            folderflags[name] = flags
        for name in sorted(folderflags.keys()):
            print '%-30s %s' % (name, ' '.join(folderflags[name]))
        print

        reply = raw_input('Type a folder name, or "q" to quit: ').strip()
        if reply.lower().startswith('q'):
            break
        if reply in folderflags:
            explore_folder(c, reply)
        else:
            print 'Error: no folder named', repr(reply)

if __name__ == '__main__':
    explore_account(c)
    ```

You can see that the outer function uses a simple `list_folders()` call to present the user with a list
of his or her mail folders, like some of the program listings we have seen already. Each folder’s IMAP
flags are also displayed. This lets the program give the user a choice between folders:
```
INBOX \HasNoChildren
Receipts \HasNoChildren
Travel \HasNoChildren
Work \HasNoChildren
Type a folder name, or "q" to quit:
``
Once a user has selected a folder, things become more interesting: a summary has to be printed for
each message.Note that it is careful to use BODY.PEEK instead of BODY to fetch these items, since the IMAP
server would otherwise mark the messages as \Seen merely because they had been displayed in a
summary.
The results of this `fetch()` call are printed to the screen once an e-mail folder has been selected:
```
2703 2010-09-28 21:32:13 19129 bytes \Seen
From: Brandon Craig Rhodes
  Subject: Digested Articles
2704 2010-09-28 23:03:45 15354 bytes
  Subject: Re: [venv] Building a virtual environment for offline testing
  From: "W. Craig Trader"
2705 2010-09-29 08:11:38 10694 bytes
  Subject: Re: [venv] Building a virtual environment for offline testing
  From: Hugo Lopes Tavares
Folder INBOX - type a message UID, or "q" to quit:
```
As you can see, the fact that several items of interest can be supplied to the IMAP `fetch()` command
lets us build fairly sophisticated message summaries with only a single round-trip to the server.
One final note about the fetch() command: it lets you not only pull just the parts of a message that
you need at any given moment, but also truncate them in case they are quite long and you just want to
provide an excerpt from the beginning to tantalize the user.
