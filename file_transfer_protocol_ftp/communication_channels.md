## Communication Channels

FTP is unusual because, by default, it actually uses two TCP connections during operation. One
connection is the control channel, which carries commands and the resulting acknowledgments or error
codes. The second connection is the data channel, which is used solely for transmitting file data or other
blocks of information, such as directory listings. Technically, the data channel is full duplex, meaning
that it allows files to be transmitted in both directions simultaneously. However, in actual practice, this
capability is rarely used.

The process of downloading a file from an FTP server ran mostly like this:

1. First, the FTP client establishes a command connection by connecting to the
FTP port on the server.
2. The client authenticates itself, usually with username and password.
3. The client changes directory on the server to where it wants to deposit or retrieve files.
4. The client begins listening on a new port for the data connection, and then
informs the server about that port.
5. The server connects to the port the client requested.
6. The file is transmitted.
7. The data connection is closed.

FTP also supports what is known as *passive mode*. In this scenario,
the data connection is made backward: the server opens an extra port, and tells the client to make the
second connection. Other than that, everything behaves the same way.
