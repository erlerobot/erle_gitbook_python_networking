## Introducing the SMTP Library

Python’s built-in SMTP implementation is in the Python Standard Library module [smtplib](https://docs.python.org/2/library/smtplib.html?highlight=smtplib#smtplib)Python’s built-in SMTP implementation is in the Python Standard Library module smtplib, which makes
it easy to do simple tasks with SMTP.

In the examples that follow, the programs are designed to take several command-line arguments:
the name of an SMTP server, a sender address, and one or more recipient addresses. Please use them
cautiously; name only an SMTP server that you yourself run or that you know will be happy receiving
your test messages, lest you wind up getting an IP address banned for sending spam!
If you don’t know where to find an SMTP server, you might try running a mail daemon like postfix
or exim locally and then pointing these example programs at localhost. Many UNIX, Linux, and Mac OS
X systems have an SMTP server like one of these already listening for connections from the local
machine.

Otherwise, consult your network administrator or Internet provider to obtain a proper hostname
and port. Note that you usually cannot just pick a mail server at random; many store or forward mail
only from certain authorized clients.
So, take a look at simple.py for a very simple SMTP program:
```python
import sys, smtplib

if len(sys.argv) < 4:
    print "usage: %s server fromaddr toaddr [toaddr...]" % sys.argv[0]
    sys.exit(2)

server, fromaddr, toaddrs = sys.argv[1], sys.argv[2], sys.argv[3:]

message = """To: %s
From: %s
Subject: Test Message from simple.py

Hello,

This is a test message sent to you from the simple.py program.
""" % (', '.join(toaddrs), fromaddr)

s = smtplib.SMTP(server)
s.sendmail(fromaddr, toaddrs, message)

print "Message successfully sent to %d recipient(s)" % len(toaddrs)
```

So, take a look at `simple.py` for a very simple SMTP program.
```
python
import sys, smtplib

if len(sys.argv) < 4:
    print "usage: %s server fromaddr toaddr [toaddr...]" % sys.argv[0]
    sys.exit(2)

server, fromaddr, toaddrs = sys.argv[1], sys.argv[2], sys.argv[3:]

message = """To: %s
From: %s
Subject: Test Message from simple.py

Hello,

This is a test message sent to you from the simple.py program.
""" % (', '.join(toaddrs), fromaddr)

s = smtplib.SMTP(server)
s.sendmail(fromaddr, toaddrs, message)

print "Message successfully sent to %d recipient(s)" % len(toaddrs)
```
It starts by generating a simple message from the user’s command-line arguments . Then it
creates an `smtplib.SMTP` object that connects to the specified server. Finally, all that’s required is a call to
`sendmail()`. If that returns successfully, then you know that the message was sent.

When you run the program, it will look like this:
```
root@erlerobot:~/Python_files# python simple.py localhost sender@example.com recipient@example.com
Message successfully sent to 2 recipient(s)
```
Thanks to the hard work that the authors of the Python Standard Library have put into the
`sendmail()` method, it might be the only SMTP call you ever need.
