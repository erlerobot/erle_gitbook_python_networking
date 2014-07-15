## Authenticated SMTP

We reach the topic of Authenticated SMTP, where your ISP, university, or company e-mail server
needs you to log in with a username and password to prove that you are not a spammer before they
allow you to send e-mail.

For maximum security, TLS should be used in conjunction with authentication; otherwise your
password (and username, for that matter) will be visible to anyone observing the connection. The proper
way to do this is to establish the TLS connection first, and then send your authentication information
only over the encrypted communications channel.

But using authentication itself is simple; `smtplib` provides a `login()` function that takes a username
and a password. `login.py` shows an example. To avoid repeating code already shown in previous
listings, this listing does not take the advice of the previous paragraph, and sends the username and
password over an un-authenticated connection that will send them in the clear.
```python
import sys, smtplib, socket
from getpass import getpass

if len(sys.argv) < 4:
    print "Syntax: %s server fromaddr toaddr [toaddr...]" % sys.argv[0]
    sys.exit(2)

server, fromaddr, toaddrs = sys.argv[1], sys.argv[2], sys.argv[3:]

message = """To: %s
From: %s
Subject: Test Message from simple.py

Hello,

This is a test message sent to you from the login.py program
in Foundations of Python Network Programming.
""" % (', '.join(toaddrs), fromaddr)

sys.stdout.write("Enter username: ")
username = sys.stdin.readline().strip()
password = getpass("Enter password: ")

try:
    s = smtplib.SMTP(server)
    try:
        s.login(username, password)
    except smtplib.SMTPException, e:
        print "Authentication failed:", e
        sys.exit(1)
    s.sendmail(fromaddr, toaddrs, message)
except (socket.gaierror, socket.error, socket.herror,
        smtplib.SMTPException), e:
    print " *** Your message may not have been sent!"
    print e
    sys.exit(1)
else:
    print "Message successfully sent to %d recipient(s)" % len(toaddrs)
    ```
You can run this program just like the previous examples. If you run it with a server that does
support authentication, you will be prompted for a username and password. If they are accepted, then
the program will proceed to transmit your message.
