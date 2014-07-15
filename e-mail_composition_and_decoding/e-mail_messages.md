## E-mail Messages

Each traditional e-mail message contains two distinct parts: headers and the body. Here is a very
simple e-mail message so that you can see what the two sections look like:
```
From: Jane Smith <jsmith@example.com>
To: Alan Jones <ajones@example.com>
Subject: Testing This E-Mail Thing

Hello Alan,
This is just a test message. Thanks.
```
The first section is called the headers, which contain all of the metadata about the message, like the
sender, the destination, and the subject of the message —everything except the text of the message itself.
The body then follows and contains the message text itself.
There are three basic rules of Internet e-mail formatting:

- At least during actual transmission, every line of an e-mail message should be
terminated by the two-character sequence carriage return, newline, represented
in Python by '\r\n'. E-mail clients running on your laptop or desktop machine
tend to make different decisions about whether to store messages in this format,
or replace these two-character line endings with whatever ending is native to your
operating system.


- The first few lines of an e-mail are headers, which consist of a header name, a
colon, a space, and a value. A header can be several lines long by indenting the
second and following lines from the left margin as a signal that they belong to the
header above them.


- The headers end with a blank line (that is, by two line endings back-to-back
without intervening text) and then the message body is everything else that
follows. The body is also sometimes called the payload.

The headers are there
for the benefit of the person who reads the e-mail message, and the most important headers are these:

- From: This identifies the message sender. It can also, in the absence of a Reply-to
header, be used as the destination when the reader clicks the e-mail client’s
“Reply” button.


- Reply-To: This sets an alternative address for replies, in case they should go to
someone besides the sender named in the From header.


- Subject: This is a short several-word description of the e-mail’s purpose, used by
most clients when displaying whole mailboxes full of e-mail messages.


- Date: This is a header that can be used to sort a mailbox in the order in which emails
arrived.


- Message-ID and In-Reply-To: Each ID uniquely identifies a message, and these IDs
are then used in e-mail replies to specify exactly which message was being replied to.
This can help sophisticated mail readers perform “threading,” arranging messages so
that replies are grouped directly beneath the messages to which they reply.
