## Composing MIME Attachments

We will start by looking at how to create [MIME](https://docs.python.org/2/library/email.mime.html) messages. To compose a message with attachments, you
will generally follow these steps:

1. Create a [MIMEMultipart](https://docs.python.org/2/library/email.mime.html?highlight=mimemultipart#email.mime.multipart.MIMEMultipart) object and set its message headers.
2. Create a [MIMEText](https://docs.python.org/2/library/email.mime.html?highlight=imetext#email.mime.text.MIMEText) object with the message body text and attach it to the
MIMEMultipart object.
3. Create appropriate MIME objects for each attachment and attach them to the
MIMEMultipart object.
4. Finally, call` as_string()` on the MIMEMultipart object to write out the resulting message.

Take a look at `mime_gen_basic.py` for a program that implements this algorithm. You can see that parts of
the code look similar to logic that we used to generate a traditional e-mail. After creating the message
and its text body, the program loops over each file given on the command line and attaches it to the
growing message.
```python

from email.mime.base import MIMEBase
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from email import utils, encoders
import mimetypes, sys

def attachment(filename):
    fd = open(filename, 'rb')
    mimetype, mimeencoding = mimetypes.guess_type(filename)
    if mimeencoding or (mimetype is None):
        mimetype = 'application/octet-stream'
    maintype, subtype = mimetype.split('/')
    if maintype == 'text':
        retval = MIMEText(fd.read(), _subtype=subtype)
    else:
        retval = MIMEBase(maintype, subtype)
        retval.set_payload(fd.read())
        encoders.encode_base64(retval)
    retval.add_header('Content-Disposition', 'attachment',
            filename = filename)
    fd.close()
    return retval

message = """Hello,

This is a test message.

-- Anonymous"""

msg = MIMEMultipart()
msg['To'] = 'recipient@example.com'
msg['From'] = 'Test Sender <sender@example.com>'
msg['Subject'] = 'Test Message'
msg['Date'] = utils.formatdate(localtime = 1)
msg['Message-ID'] = utils.make_msgid()

body = MIMEText(message, _subtype='plain')
msg.attach(body)
for filename in sys.argv[1:]:
    msg.attach(attachment(filename))
print msg.as_string()
```
The `attachment()` function does the work of creating a message attachment object. First, it determines
the MIME type of each file by using Python’s built-in [mimetypes](https://docs.python.org/2/library/mimetypes.html?highlight=mimetypes) module. If the type can’t be determined, or
it will need a special kind of encoding, then a type is declared that promises only that the data is made of a
“stream of octets” (sequence of bytes) but without any further promise about what they mean.
If the file is a text document whose MIME type starts with text/, a MIMEText object is created to handle
it; otherwise, a [MIMEBase]((https://docs.python.org/2/library/email.mime.html?highlight=mimebase#email.mime.base.MIMEBase)) generic object is created. In the latter case, the contents are assumed to be binary,
so they are encoded with base-64. Finally, an appropriate Content-Disposition header is added to that
section of the MIME file so that mail readers will know that they are dealing with an attachment.

The result of running this program is shown below :
```
root@erlerobot:~/Python_files# echo "This is a test" > test.txt
root@erlerobot:~/Python_files# gzip < test.txt > test.txt.gz
root@erlerobot:~/Python_files# ./mime_gen_basic.py test.txt test.txt.gz
Content-Type: multipart/mixed; boundary="===============1623374356=="
MIME-Version: 1.0
To: recipient@example.com
From: Test Sender <sender@example.com>
Subject: Test Message
Date: Mon, 14 Jul 2014 14:36:07 +0200
Message-ID:  <20140714123150.987.14344@root-erlerobot.local>
--===============1623374356==
Content-Type: text/plain; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
Hello,
This is a test message.
-- Anonymous
--===============1623374356==
Content-Type: text/plain; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
Content-Disposition: attachment; filename="test.txt"
This is a test
--===============1623374356==
Content-Type: application/octet-stream
MIME-Version: 1.0
Content-Transfer-Encoding: base64
Content-Disposition: attachment; filename="test.txt.gz"
H4sIAP3o2D8AAwvJyCxWAKJEhZLU4hIuAIwtwPoPAAAA
--===============1623374356==--

```

The message starts off looking quite similar to the traditional ones we created earlier; you can see
familiar headers like To, From, and Subject just like before. Note the Content-Type line, however: it
indicates multipart/mixed. That tells the mail reader that the body of the message contains multiple
MIME parts, and that the string containing equals signs will be the separator between them.
Next comes the message’s first part. Notice that it has its own Content-Type header! The second part
looks similar to the first, but has an additional Content-Disposition header; this will signal most e-mail
readers that the part should be displayed as a file that the user can save rather than being immediately
displayed to the screen. Finally comes the part containing the binary file, encoded with base-64, which
makes it not directly readable.









