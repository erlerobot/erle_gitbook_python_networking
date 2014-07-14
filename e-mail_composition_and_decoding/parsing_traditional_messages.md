## Parsing Traditional Messages

What happens when you receive an
incoming message as a raw block of text and want to look inside? Well, the `email` module also provides
support for parsing e-mail messages, re-constructing the same Message object that would have been
used to create the message in the first place. (Of course, it does not matter whether the e-mail you are
parsing was originally created in Python through the Message class, or whether some other e-mail
program created it; the format is standard, so Python’s parsing should work either way.)
After parsing the message, you can easily access individual headers and the body of the message
using the same conventions as you used to create messages: headers look like the dictionary key-values
of the Message, and the body can be fetched with a function.

A simple example of a parser is shown in
trad_parse.py. All of the actual parsing takes place in the one-line function `message_from_file()`;
everything else in the program listing is simply an illustration of how a Message object can be mined for
headers and data.
```python
import email

banner = '-' * 48
popular_headers = ('From', 'To', 'Subject', 'Date')
msg = email.message_from_file(open('message.txt'))
headers = sorted(msg.keys())

print banner
for header in headers:
    if header not in popular_headers:
        print header + ':', msg[header]
print banner
for header in headers:
    if header in popular_headers:
        print header + ':', msg[header]

print banner
if msg.is_multipart():
    print "This program cannot handle MIME multipart messages."
else:
    print msg.get_payload()
    ```
The output should be like this

```
root@erlerobot:~/Python_files# python trad_parse.py
------------------------------------------------
Message-ID: <20140714123150.987.14344@root-erlerobot.local>
------------------------------------------------
Date:  Mon, 14 Jul 2014 14:33:54 +0200
From: Test Sender <sender@example.com>
Subject: Test Message, Chapter 12
To: recipient@example.com
------------------------------------------------
Hello,
This is a test message.
-- Anonymous
root@erlerobot:~/Python_files#
```

As you can see, the Python Standard Library makes it quite easy both to create and then to parse
standard Internet e-mail messages. Note that the email package also offers a `message_from_string()`
function that, instead of taking a file, can simply be handed the string containing an e-mail message.
