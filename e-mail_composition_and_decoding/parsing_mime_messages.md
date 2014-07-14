## Parsing MIME Messages

Python’s `email module can read a message from a file or a string, and generate the same kind of inmemory
object tree that we were generating ourselves in the aforementioned listings. To understand the
e-mail’s content, all you have to do is step through its structure.
Show an example at `mime_structure.py`:
```python

import sys, email

def printmsg(msg, level = 0):
    prefix = "|  " * level
    prefix2 = prefix + "|"
    print prefix + "+ Message Headers:"
    for header, value in msg.items():
        print prefix2, header + ":", value
    if msg.is_multipart():
        for item in msg.get_payload():
            printmsg(item, level + 1)

msg = email.message_from_file(sys.stdin)
printmsg(msg)
```
This program is short and simple. For each object it encounters, it checks to see if it is multipart; if
so, the children of that object are displayed as well.
Individual parts of a message can easily be extracted. You will recall that there are several ways that
message data may be encoded; fortunately, the email module can decode them all! `mime_decode.py`shows a
program that will let you decode and save any component of a MIME message:
```python
import sys, email
counter = 0
parts = []

def printmsg(msg, level = 0):
    global counter
    l = "|  " * level
    if msg.is_multipart():
        print l + "Found multipart:"
        for item in msg.get_payload():
            printmsg(item, level + 1)
    else:
        disp = ['%d. Decodable part' % (counter + 1)]
        if 'content-type' in msg:
            disp.append(msg['content-type'])
        if 'content-disposition' in msg:
            disp.append(msg['content-disposition'])
        print l + ", ".join(disp)
        counter += 1
        parts.append(msg)

inputfd = open(sys.argv[1])
msg = email.message_from_file(inputfd)
printmsg(msg)

while 1:
    print "Select part number to decode or q to quit: "
    part = sys.stdin.readline().strip()
    if part == 'q':
        sys.exit(0)
    try:
        part = int(part)
        msg = parts[part - 1]
    except:
        print "Invalid selection."
        continue

    print "Select file to write to:"
    filename = sys.stdin.readline().strip()
    try:
        fd = open(filename, 'wb')
    except:
        print "Invalid filename."
        continue

    fd.write(msg.get_payload(decode = 1))
    ```

This program steps through the message, like the last example. We skip asking the user about
message components that are multipart because those exist only to contain other message objects, like
text and attachments; multipart sections have no actual payload of their own.
