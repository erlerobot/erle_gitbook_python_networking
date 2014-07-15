# Searching and Manipulating Messages


Searching is another issue that is very important for a protocol designed to let you keep all your mail on
the mail server itself: without search, an e-mail client would have to download all of a user’s mail anyway
the first time he or she wanted to perform a full-text search to find an e-mail message.
The essence of search is simple: you call the `search()` method on an IMAP client instance, and are
returned the UIDs (assuming, of course, that you accept the IMAPClient default of use_uid=True for your
client) of the messages that match your criteria:
```python
>>> c.select_folder('INBOX')
>>> c.search('SINCE 20-Aug-2010 TEXT Apress')
[2590L, 2652L, 2653L, 2654L, 2655L, 2699L]
```

There are many criteria that you can combine in order to form a query. Like the rest of IMAP, they
are specified in RFC 3501. Some criteria are quite simple, and refer to binary attributes like flags:
```
ALL: Every message in the mailbox
UID (id, ...): Messages with the given UIDs
LARGER n: Messages more than n octets in length
SMALLER m: Messages less than m octets in length
ANSWERED: Have the flag \Answered
DELETED: Have the flag \Deleted
DRAFT: Have the flag \Draft
FLAGGED: Have the flag \Flagged
KEYWORD flag: Have the given keyword flag set
NEW: Have the flag \Recent
OLD: Lack the flag \Recent
UNANSWERED: Lack the flag \Answered
UNDELETED: Lack the flag \Deleted
UNDRAFT: Lack the flag \Draft
UNFLAGGED: Lack the flag \Flagged
UNKEYWORD flag: Lack the given keyword flag
UNSEEN: Lack the flag \Seen
```

There are two sets of criteria for
dates, depending on which date you want to query by: the internal Date header(sned date) and the at whcich arrived at the IMAP server.

Finally, there are two search operations that refer to the text of the message itself—these are the big
workhorses that support full-text search of the kind your users are probably expecting when they type
into a search field in an e-mail client:
```
BODY string: The message body must contain the string.
TEXT string: The entire message, either body or header, must contain the string somewhere.
```

######Manipulating Folders and Messages

Creating or deleting folders is done quite simply in IMAP, by providing the name of the folder:
```
c.create_folder('Personal')
c.delete_folder('Work')
```
Some IMAP servers or configurations may not permit these operations, or may have restrictions on
naming; be sure to have error checking in place when calling them.
There are two operations that can create new e-mail messages in your IMAP account besides the
“normal” means of waiting for people to send them to you.
First, you can copy an existing message from its home folder over into another folder. Start by using
`select_folder()` to visit the folder where the messages live, and then run the copy method like this:
```
c.select_folder('INBOX')
c.copy([2653L, 2654L], 'TODO')
```
Finally, it is possible to add a message to a mailbox with IMAP. You do not need to send the message
first with SMTP; IMAP is all that is needed. Adding a message is a simple process, though there are a
couple of things to be aware of.

You must also be cautious in how carefully you change the line endings, because some
messages may use '\r\n' somewhere inside despite using only '\n' for the first few dozen lines, and
IMAP clients have been known to fail if a message uses both different line endings! The solution is a
simple one, thanks to Python’s powerful `splitlines() `string method that recognizes all three possible
line endings; simply call the function on your message and then re-join the lines with the standard line
ending:
```python
>>> 'one\rtwo\nthree\r\nfour'.splitlines()
['one', 'two', 'three', 'four']
>>> '\r\n'.join('one\rtwo\nthree\r\nfour'.splitlines())
'one\r\ntwo\r\nthree\r\nfour'
```
The actual act of appending a message, once you have the line endings correct, is to call the
`append()` method on your IMAP client:
```
c.append('INBOX', my_message)
```
You can also supply a list of flags as a keyword argument, as well as a `msg_time` to be used as its
arrival time by passing a normal Python datetime object.
