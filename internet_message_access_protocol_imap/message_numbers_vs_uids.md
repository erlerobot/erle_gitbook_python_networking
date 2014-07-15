## Message Numbers vs. UIDs

IMAP provides two different ways to refer to a specific message within a folder: by a temporary message
number (which typically goes 1, 2, 3, and so forth) or by a UID (unique identifier). The difference
between the two lies with persistence. Message numbers are assigned right when you select the folder.
This means they can be pretty and sequential, but it also means that if you revisit the same folder later,
then a given message may have a different number. For programs such as live mail readers or simple
download scripts, this behavior (which is the same as POP) is fine; you do not need the numbers to stay
the same.
But a UID, by contrast, is designed to remain the same even if you close your connection to the
server and do not reconnect again for another week. If a message had UID 1053 today, then the same
message will have UID 1053 tomorrow, and no other message in that folder will ever have UID 1053. If
you are writing a synchronization tool, this behavior is quite useful! It will allow you to verify with 100%
percent certainty that actions are being taken against the correct message. This is one of the things that
make IMAP so much more fun than POP.

Most IMAP commands that work with specific messages can take either message numbers or UIDs.
Normally, `IMAPClient` always uses UIDs and ignores the temporary message numbers assigned by IMAP.
But if you want to see the temporary numbers instead, simply instantiate `IMAPClient` with a
`use_uid=False` argument—or, you can even set the value of the class’s `use_uid` attribute to False and
True on the fly during your IMAP session.
