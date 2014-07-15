## Getting Information from EHLO

Sometimes it is nice to know about what kind of messages a remote SMTP server will accept. For
instance, most SMTP servers have a limit on what size message they permit, and if you fail to check first,
then you may transmit a very large message only to have it rejected when you have completed
transmission.

Some servers do not support ESMTP. On
those servers, EHLO will just return an error. In that case, you must send a HELO command instead.
In the previous examples, we used `sendmail()` immediately after creating our SMTP object, so
smtplib had to send its own “hello” message to the server. But if it sees you attempt to send the EHLO or
HELO command on your own, then `sendmail()` will no longer attempt to send these commands itself.
`ehlo.py` shows a program that gets the maximum size from the server, and returns an error
before sending if a message would be too large.
```python

import sys, smtplib, socket

if len(sys.argv) < 4:
    print "usage: %s server fromaddr toaddr [toaddr...]" % sys.argv[0]
    sys.exit(2)

server, fromaddr, toaddrs = sys.argv[1], sys.argv[2], sys.argv[3:]

message = """To: %s
From: %s
Subject: Test Message from simple.py

Hello,

This is a test message sent to you from the ehlo.py program.
""" % (', '.join(toaddrs), fromaddr)

try:
    s = smtplib.SMTP(server)
    code = s.ehlo()[0]
    uses_esmtp = (200 <= code <= 299)
    if not uses_esmtp:
        code = s.helo()[0]
        if not (200 <= code <= 299):
            print "Remote server refused HELO; code:", code
            sys.exit(1)

    if uses_esmtp and s.has_extn('size'):
        print "Maximum message size is", s.esmtp_features['size']
        if len(message) > int(s.esmtp_features['size']):
            print "Message too large; aborting."
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
If you run this program, and the remote server provides its maximum message size, then the
program will display the size on your screen and verify that its message does not exceed that size before
sending.
Here is what running this program might look like:
```
root@erlerobot:~/Python_files# python ehlo.py localhost foo@example.com jgoerzen@complete.org Maximum message size is 10240000
Message successfully sent to 1 recipient(s)
```
Take a look at the part of the code that verifies the result from a call to `ehlo()` or `helo()`. Those two
functions return a list; the first item in the list is a numeric result code from the remote SMTP server.
