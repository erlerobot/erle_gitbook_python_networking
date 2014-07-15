# Post Office Protocol (POP)

The Post Office Protocol, is a simple protocol that is used to download e-mail from a mail server,
and is typically used through an e-mail client like Thunderbird or Outlook.
POP does not support multiple mailboxes
on the remote side, nor does it provide any reliable, persistent message identification. This means that
you cannot use POP as a protocol for mail synchronization.
The Python Standard Library provides the [poplib](https://docs.python.org/2/library/poplib.html?highlight=poplib#poplib) module, which provides a convenient interface for
using POP. In this chapter, you will learn how to use poplib to connect to a POP server, gather summary
information about a mailbox, download messages, and delete the originals from the server.
