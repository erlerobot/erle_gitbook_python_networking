## Understanding MIME

So far we have discussed e-mail messages that are plain text: the characters after the blank line that ends
the headers are to be presented literally to the user as the content of the e-mail message. Today, only a
fraction of the messages sent across the Internet are so simple.

The Multipurpose Internet Mail Extensions ([MIME](https://docs.python.org/2/library/email.mime.html?highlight=mimebase#email.mime.base.MIMEBase)) standard is a set of rules for encoding data,
rather than simple plain text, inside e-mails. MIME provides a system for things like attachments,
alternative message formats, and text that is stored in alternate encodings.
Because MIME messages have to be transmitted and delivered through many of the same old e-mail
services that were originally designed to handle plain-text e-mails, MIME operates by adding headers to
an e-mail message and then giving it content that looks like plain text to the machine but that can
actually be decoded by an e-mail client into HTML, images, or attachments.

The most important features of MIME are, first, that MIME supports multipart messages. A normal e-mail message, as we have seen, contains
some headers and a body. But a MIME message can squeeze several different parts into the message
body. These parts might be things to be presented to the user in order, like a plain-text message, an
image file attachment, and then a PDF attachment. Or, they could be alternative multiparts, which
represent the same content in different ways —usually, by encoding a message in both plain text and
HTML.
Second, MIME supports different transfer encodings. Traditional e-mail messages are limited to 7-
bit data, which renders them unusable for international alphabets. MIME has several ways of
transforming 8-bit data so it fits within the confines of e-mail systems:

- The “plain” encoding is the same as you would see in traditional messages, and
passes 7-bit text unmodified.


- “Base-64” is a way of encoding raw binary data that turns it into normal
alphanumeric data. Most of the attachments you send and receive —such as
images, PDFs, and ZIP files —are encoded with base-64.


- “Quoted-printable” is a hybrid that tries to leave plain English text alone so that it
remains readable in old mail readers, while also letting unusual characters be
included as well.

MIME also provides content types, which tell the recipient what kind of content is present. For
instance, a content type of text/plain indicates a plain-text message, while image/jpeg is a JPEG image.

######How MIME works

You will recall that MIME messages must work within the limited plain-text framework of traditional email
messages. To do that, the MIME specification defines some headers and some rules about
formatting the body text.

For *non-multipart messages* that are a single block of data, MIME simply adds some headers to
specify what kind of content the e-mail contains, along with its character set. But the body of the
message is still a single piece, although it might be encoded with one of the schemes already described.

For *multipart messages*, things get trickier: MIME places a special marker in the e-mail body
everywhere that it needs to separate one part from the next. Each part can then have its own limited set
of headers —which occur at the start of the part —followed by data. By convention, the most basic
content in an e-mail comes first (like a plain-text message, if one has been included), so that people
without MIME-aware readers will see the plain text immediately without having to scroll down through
dozens or hundreds of pages of MIME data.

