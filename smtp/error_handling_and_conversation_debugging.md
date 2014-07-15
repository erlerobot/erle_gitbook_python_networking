## Error Handling and Conversation Debugging

There are several different exceptions that might be raised while you’re programming with `smtplib`. They
are:

- `socket.gaierror` for errors looking up address information.


- `socket.error` for general I/O and communication problems.


- `socket.herror` for other addressing errors.


- `smtplib.SMTPException` or a subclass of it for SMTP conversation problems.

The `smtplib` module also provides a way to get a series of detailed messages about the steps it takes
to send an e-mail. To enable that level of detail, you can call
`smtpobj.set_debuglevel(1)`
With this option, you should be able to track down any problems.
Take a a look at `debug.py` for an
example program that provides basic error handling and debugging.
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

This is a test message sent to you from the debug.py program.
""" % (', '.join(toaddrs), fromaddr)

try:
    s = smtplib.SMTP(server)
    s.set_debuglevel(1)
    s.sendmail(fromaddr, toaddrs, message)
except (socket.gaierror, socket.error, socket.herror,
        smtplib.SMTPException), e:
    print " *** Your message may not have been sent!"
    print e
    sys.exit(1)
else:
    print "Message successfully sent to %d recipient(s)" % len(toaddrs)
    ```

This program looks similar to the last one. However, the output will be very different.
```
root@erlerobot:~/Python_files# python debug.py localhost foo@example.com jgoerzen@complete.org
send: 'ehlo localhost\r\n'
reply: '250-localhost\r\n'
reply: '250-PIPELINING\r\n'
reply: '250-SIZE 20480000\r\n'
reply: '250-VRFY\r\n'
reply: '250-ETRN\r\n'
reply: '250-STARTTLS\r\n'
...
Message successfully sent to 1 recipient(s)
```
From this example, you can see the conversation that `smtplib` is having with the SMTP server over
the network.Let’s look at what’s happening:
First, the client (the `smtplib` library) sends an EHLO command (an “extended” successor to a more
ancient command that was named, more readably, HELO) with your hostname in it. The remote server
responds with its hostname, and lists any optional SMTP features that it supports.
Next, the client sends the mail from command, which states the “envelope sender” e-mail address
and the size of the message. The server at this moment has the opportunity to reject the message (for
example, because it thinks you are a spammer); but in this case, it responds with 250 Ok. (Note that in
this case, the code 250 is what matters; the remaining text is just a human-readable comment and varies
from server to server.)
Then the client sends a `rcpt` to command, with the “envelope recipient” that we talked so much
about earlier in this chapter; you can finally see that, indeed, it is transmitted separately from the text of
the message itself when using the SMTP protocol. If you were sending the message to more than one
recipient, they would each be listed on the rcpt to line.
Finally, the client sends a data command, transmits the actual message (using verbose carriagereturn-
linefeed line endings, you will note, per the Internet e-mail standard), and finishes the
conversation.


The `smtplib` module is doing all this automatically for you in this example. In the rest of the chapter,
we will look at how to take more control of the process so you can take advantage of some more
advanced features.
