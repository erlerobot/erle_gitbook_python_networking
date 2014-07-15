## MIME Alternative Parts

MIME “alternative” parts let you generate multiple versions of a single document. The user’s mail reader
will then automatically decide which one to display, depending on which content type it likes best; some
mail readers might even show the user radio buttons, or a menu, and let them choose.
The process of creating alternatives is similar to the process for attachments, and is illustrated in `mime_gen_alt.py`:
```python

from email.mime.base import MIMEBase
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from email import utils, encoders

def alternative(data, contenttype):
    maintype, subtype = contenttype.split('/')
    if maintype == 'text':
        retval = MIMEText(data, _subtype=subtype)
    else:
        retval = MIMEBase(maintype, subtype)
        retval.set_payload(data)
        encoders.encode_base64(retval)
    return retval

messagetext = """Hello,

This is a *great* test message.

-- Anonymous"""
messagehtml = """Hello,<P>
This is a <B>great</B> test message from Chapter 12.  I hope you enjoy
it!<P>
-- <I>Anonymous</I>"""


msg = MIMEMultipart('alternative')
msg['To'] = 'recipient@example.com'
msg['From'] = 'Test Sender <sender@example.com>'
msg['Subject'] = 'Test Message, Chapter 12'
msg['Date'] = utils.formatdate(localtime = 1)
msg['Message-ID'] = utils.make_msgid()

msg.attach(alternative(messagetext, 'text/plain'))
msg.attach(alternative(messagehtml, 'text/html'))
print msg.as_string()
```
Notice the differences between an alternative message and a message with attachments! With the
alternative message, no Content-Disposition header is inserted. Also, the MIMEMultipart object is passed
the alternative subtype to tell the mail reader that all objects in this multipart are alternative views of
the same thing.
Note again that it is always most polite to include the plain-text object first for people with ancient
or incapable mail readers, which simply show them the entire message as text.

