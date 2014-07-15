## Flagging and Deleting Messages

######Flagging

You might have noticed, while trying out  `simple_client.py` or reading its example output just shown, that
IMAP marks messages with attributes called “flags,” which typically take the form of a backslashprefixed
word, like \Seen for one of the messages just cited. Several of these are standard, and are
defined in RFC 3501 for use on all IMAP servers. Here is what the most important ones mean:

- \Answered: The user has replied to the message.


- \Draft: The user has not finished composing the message.


- \Flagged: The message has somehow been singled out specially; the purpose and
meaning of this flag vary between mail readers.


- \Recent: No IMAP client has seen this message before. This flag is unique, in that
the flag cannot be added or removed by normal commands; it is automatically
removed after the mailbox is selected.


- \Seen: The message has been read.


The IMAPClient library supports several methods for working with flags. The simplest retrieves the
flags as though you had done a `fetch()` asking for 'FLAGS', but goes ahead and removes the dictionary
around each answer:
```python
>>> c.get_flags(2703)
{2703: ('\\Seen',)}
```
There are also calls to add and remove flags from a message:
```
c.remove_flags(2703, ['\\Seen'])
c.add_flags(2703, ['\\Answered'])
```
In case you want to completely change the set of flags for a particular message without figuring out
the correct series of adds and removes, you can use `set_flags()` to unilaterally replace the whole list of
message flags with a new one:
```
c.set_flags(2703, ['\\Seen', '\\Answered'])
```
Any of these operations can take a list of message UIDs instead of the single UID shown in these
examples.

######Deleting Messages

One last interesting use of flags is that it is how IMAP supports message deletion. The process, for safety,
takes two steps: first the client marks one or more messages with the \Delete flag; then it calls `expunge()`
to perform the deletions as a single operation.
The IMAPClient library does not make you do this by hand, however (though that would work);
instead it hides the fact that flags are involved behind a simple `delete_messages()` routine that marks the
messages for you. It still has to be followed by `expunge()` if you actually want the operation to take effect,
though:
```
c.delete_messages([2703, 2704])
c.expunge()
```
