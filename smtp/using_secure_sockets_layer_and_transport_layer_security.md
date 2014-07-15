## Using Secure Sockets Layer and Transport Layer Security

E-mails sent in plain text over SMTP can be read by anyone with access to an Internet
gateway or router across which the packets happen to pass. The best solution to this problem is to
encrypt each e-mail with a public key whose private key is possessed only by the person to whom you
are sending the e-mail; there are freely available systems such as PGP and GPG for doing exactly this. But
regardless of whether the messages themselves are protected, individual SMTP conversations between
particular pairs of machines can be encrypted and authenticated using a method known as SSL/TLS.

The general procedure for using TLS in SMTP is as follows:

1. Create the SMTP object, as usual.
2. Send the EHLO command. If the remote server does not support EHLO, then it will
not support TLS.
3. Check `s.has_extn()` to see if starttls is present. If not, then the remote server
does not support TLS and the message can only be sent normally, in the clear.
4. Call `starttls()` to initiate the encrypted channel.
5. Call `ehlo()` a second time; this time, it’s encrypted.
6. Finally, send your message.

The first question you have to ask yourself when working with TLS is whether you should return an
error if TLS is not available. Depending on your application, you might want to raise an error for any of
the following:

- There is no support for TLS on the remote side.


- The remote side fails to establish a TLS session properly.


- The remote server presents a certificate that cannot be validated.


`tls.py` acts as a TLS-capable general-purpose client. It will connect to a server and use TLS if it
can; otherwise, it will fall back and send the message as usual. (But it will die with an error if the attempt
to start TLS fails while talking to an ostensibly capable server).

```python
import sys, smtplib, socket

if len(sys.argv) < 4:
    print "Syntax: %s server fromaddr toaddr [toaddr...]" % sys.argv[0]
    sys.exit(2)

server, fromaddr, toaddrs = sys.argv[1], sys.argv[2], sys.argv[3:]

message = """To: %s
From: %s
Subject: Test Message from simple.py

Hello,

This is a test message sent to you from the tls.py program
in Foundations of Python Network Programming.
""" % (', '.join(toaddrs), fromaddr)

try:
    s = smtplib.SMTP(server)
    code = s.ehlo()[0]
    uses_esmtp = (200 <= code <= 299)
    if not uses_esmtp:
        code = s.helo()[0]
        if not (200 <= code <= 299):
            print "Remove server refused HELO; code:", code
            sys.exit(1)

    if uses_esmtp and s.has_extn('starttls'):
        print "Negotiating TLS...."
        s.starttls()
        code = s.ehlo()[0]
        if not (200 <= code <= 299):
            print "Couldn't EHLO after STARTTLS"
            sys.exit(5)
        print "Using TLS connection."
    else:
        print "Server does not support TLS; using normal connection."
    s.sendmail(fromaddr, toaddrs, message)

except (socket.gaierror, socket.error, socket.herror,
        smtplib.SMTPException), e:
    print " *** Your message may not have been sent!"
    print e
    sys.exit(1)
else:
    print "Message successfully sent to %d recipient(s)" % len(toaddrs)
    ```

 If you run this program and give it a server that understands TLS, the output will look like this:

 ```
root@erlerobot:~/Python_files# python tls.py jgoerzen@complete.org jgoerzen@complete.org
Negotiating TLS....
Using TLS connection.
Message successfully sent to 1 recipient(s)
```
Notice that the call to `sendmail()`  in these last few listings is the same, regardless of whether TLS is
used.
