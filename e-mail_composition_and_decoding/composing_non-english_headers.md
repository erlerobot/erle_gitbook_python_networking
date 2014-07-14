## Composing Non-English Headers

Although you have seen how MIME can encode message body parts with base-64 to allow 8-bit data to
pass through, that does not solve the problem of special characters in headers. For instance, if your
name was Michael Müller (with an umlaut over the “u”), you would have trouble representing your
name accurately in your own alphabet. The “u” would come out bare.
Therefore, MIME provides a way to encode data in headers. Take a look at `mime_headers.py`for how to
do it in Python.
```python

from email.mime.text import MIMEText
from email.header import Header

message = """Hello,

This is a test message .

-- Anonymous"""

msg = MIMEText(message)
msg['To'] = 'recipient@example.com'
fromhdr = Header()
fromhdr.append(u"Michael M\xfcller")
fromhdr.append('<mmueller@example.com>')
msg['From'] = fromhdr
msg['Subject'] = 'Test Message'

print msg.as_string()
```
The code '\xfc' in the Unicode string (strings in Python source files that are prefixed with u can
contain arbitrary Unicode characters, rather than being restricted to characters whose value is between
0 and 255).
```
root@erlerobot:~/Python_files#./mime_headers.py
Content-Type: text/plain; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
To: recipient@example.com
From: =?iso-8859-1?q?Michael_M=FCller?= <mmueller@example.com>
Subject: Test Message
Date: Mon, 14 Jul 2014 14:46:33 +0200
Message-ID: <20140714123150.987.14344@root-erlerobot.local>
Hello,
This is a test message.
-- Anonymous
```
