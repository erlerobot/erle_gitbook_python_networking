## Composing Traditional Messages

How can we generate a traditional e-mail in Python without
having to implement the formatting details ourselves? The answer is to use the modules within the
powerful [email package](https://docs.python.org/2/library/email.html?highlight=email#email). The email package is a library for managing email messages, including MIME and other RFC 2822-based message documents.

As our first example, `trad_gen_simple.py` shows a program that generates a simple message. Note that when
you generate messages this way, manually setting the payload with the Message class, you should limit
yourself to using plain 7-bit ASCII text.

```python

from email.message import Message
text = """Hello,

This is a test message.

-- Anonymous"""

msg = Message()
msg['To'] = 'recipient@example.com'
msg['From'] = 'Test Sender <sender@example.com>'
msg['Subject'] = 'Test Message'
msg.set_payload(text)

print msg.as_string()
```

The program is simple. It creates a Message object, sets the headers and body, and prints the result.
When you run this program, you will get a nice formatted message with proper headers:
```
root@erlerobot:~/Python_files# python trad_gen_simple.py
To: recipient@example.com
From: Test Sender <sender@example.com>
Subject: Test Message

Hello,

This is a test message.

-- Anonymous
root@erlerobot:~/Python_files#
```

While technically correct, this message is actually a bit deficient when it comes to providing enough
headers to really function in the modern world. For one thing, most e-mails should have a Date header,
in a format specific to e-mail messages. Python provides an `email.utils.formatdate()` routine that will
generate dates in the right format.
You should add a Message-ID header to messages. This header should be generated in such a way
that no other e-mail, anywhere in history, will ever have the same Message-ID. This might sound
difficult, but Python provides a function to help do that as well: `email.utils.make_msgid()`.
So take a look at `trad_gen_newhdrs.py`, which fleshes out our first sample program into a more complete
example that sets these additional headers.

```python
import email.utils
from email.message import Message

message = """Hello,

This is a test message.

-- Anonymous"""

msg = Message()
msg['To'] = 'recipient@example.com'
msg['From'] = 'Test Sender <sender@example.com>'
msg['Subject'] = 'Test Message'
msg['Date'] = email.utils.formatdate(localtime = 1)
msg['Message-ID'] = email.utils.make_msgid()
msg.set_payload(message)

print msg.as_string()
```
If you run the program, you will notice two new headers in the output.
```
root@erlerobot:~/Python_files# python trad_gen_newhdrs.py
To: recipient@example.com
From: Test Sender <sender@example.com>
Subject: Test Message
Date: Mon, 14 Jul 2014 14:31:50 +0200
Message-ID: <20140714123150.987.14344@root-erlerobot.local>

Hello,

This is a test message.

-- Anonymous
root@erlerobot:~/Python_files#
```


