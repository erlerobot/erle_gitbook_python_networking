## Downloading and Deleting Messages

You should now be getting the hang of POP: when using `poplib` you get to issue small atomic commands
that always return a tuple inside which are various strings and lists of strings showing you the result. We
are now ready to actually manipulate messages! The three relevant methods, which all identify messages
using the same integer identifiers that are returned by `list()`, are these:

- `retr(num)`: This method downloads a single message and returns a tuple containing a
result code and the message itself, delivered as a list of lines. This will cause most POP
servers to set the “seen” flag for the message to “true,” barring you from ever seeing it from
POP again (unless you have another way into your mailbox that lets you set messages back
to “Unread”).


- `top(num, body_lines)`:This method returns its result in the same format as `retr()` without
marking the message as “seen.” But instead of returning the whole message, it just returns
the headers plus however many lines of the body you ask for in `body_lines`. This is useful
for previewing messages if you want to let the user decide which ones to download.


- `dele(num)`: This method marks the message for deletion from the POP server, to take place
when you quit this POP session. Typically you would do this only if the user directly
requests irrevocable destruction of the message, or if you have stored the message to disk
and used something like `fsync()` to assure the data’s safety.


To put everything together, take a look at `download-and-delete.py`, which is a fairly functional e-mail client that
speaks POP. It checks your in-box to determine how many messages there are and to learn what their
numbers are; then it uses `top()` to offer a preview of each one; and, at the user’s option, it can retrieve
the whole message, and can also delete it from the mailbox.
```

import email, getpass, poplib, sys

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
    response, listings, octets = p.list()
    for listing in listings:
        number, size = listing.split()
        print 'Message', number, '(size is', size, 'bytes):'
        print
        response, lines, octets = p.top(number, 0)
        message = email.message_from_string('\n'.join(lines))
        for header in 'From', 'To', 'Subject', 'Date':
            if header in message:
                print header + ':', message[header]
        print
        print 'Read this message [ny]?'
        answer = raw_input()
        if answer.lower().startswith('y'):
            response, lines, octets = p.retr(number)
            message = email.message_from_string('\n'.join(lines))
            print '-' * 72
            for part in message.walk():
                if part.get_content_type() == 'text/plain':
                    print part.get_payload()
                    print '-' * 72
        print
        print 'Delete this message [ny]?'
        answer = raw_input()
        if answer.lower().startswith('y'):
            p.dele(number)
            print 'Deleted.'
finally:
    p.quit()
    ```

If you run this program, you’ll see output similar to this:
```
root@erlerobot:~/Python_files# python download-and-delete.py pop.gmail.com my_gmail_acct
Message 1 (size is 1847 bytes):
From: root@server.example.com
To: Brandon Rhodes <brandon.craig.rhodes@gmail.com>
Subject: Backup complete
Date: Tue, 13 Apr 2010 16:56:43 -0700 (PDT)
Read this message [ny]?
n
Delete this message [ny]?
y
Deleted.
```
