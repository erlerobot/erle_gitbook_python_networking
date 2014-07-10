## Using sockets


The first thing to note, is that the web browser’s “client” socket and the web server’s “client” socket are identical beasts. That is, this is a “peer to peer” conversation. Or to put it another way, as the designer, you will have to decide what the rules of etiquette are for a conversation. Normally, the connecting socket starts the conversation, by sending in a request, or perhaps a signon. But that’s a design decision - it’s not a rule of sockets.

Now there are two sets of verbs to use for communication. You can use `send()` and `recv()`, or you can transform your client socket into a file-like beast and use `read()` and `write()`.  I’m not going to talk about it here, except to warn you that you need to use flush on sockets. These are buffered “files”, and a common mistake is to write something, and then read for a reply. Without a flush in there, you may wait forever for the reply, because the request may still be in your output buffer.

Now we come to the major stumbling block of sockets - `send()` and `recv()` operate on the network buffers. They do not necessarily handle all the bytes you hand them (or expect from them), because their major focus is handling the network buffers. In general, they return when the associated network buffers have been filled (send) or emptied (recv). They then tell you how many bytes they handled. It is your responsibility to call them again until your message has been completely dealt with.

When a `recv()` returns 0 bytes, it means the other side has closed (or is in the process of closing) the connection. You will not receive any more data on this connection.

A protocol like HTTP uses a socket for only one transfer. The client sends a request, then reads a reply. That’s it. The socket is discarded. This means that a client can detect the end of the reply by receiving 0 bytes.

But if you plan to reuse your socket for further transfers, you need to realize that there is no EOT on a socket. I repeat: if a socket `send()` or `recv()` returns after handling 0 bytes, the connection has been broken. If the connection has not been broken, you may wait on a `recv()` forever, because the socket will not tell you that there’s nothing more to read (for now). Now if you think about that a bit, you’ll come to realize a fundamental truth of sockets: messages must either be fixed length (yuck), or be delimited (shrug), or indicate how long they are (much better), or end by shutting down the connection. The choice is entirely yours, (but some ways are righter than others).

Assuming you don’t want to end the connection, the simplest solution is a fixed length message:

```python
class mysocket:
    '''demonstration class only
      - coded for clarity, not efficiency
    '''

    def __init__(self, sock=None):
        if sock is None:
            self.sock = socket.socket(
                socket.AF_INET, socket.SOCK_STREAM)
        else:
            self.sock = sock

    def connect(self, host, port):
        self.sock.connect((host, port))

    def mysend(self, msg):
        totalsent = 0
        while totalsent < MSGLEN:
            sent = self.sock.send(msg[totalsent:])
            if sent == 0:
                raise RuntimeError("socket connection broken")
            totalsent = totalsent + sent

    def myreceive(self):
        chunks = []
        bytes_recd = 0
        while bytes_recd < MSGLEN:
            chunk = self.sock.recv(min(MSGLEN - bytes_recd, 2048))
            if chunk == '':
                raise RuntimeError("socket connection broken")
            chunks.append(chunk)
            bytes_recd = bytes_recd + len(chunk)
        return ''.join(chunks)
        ```
The sending code here is usable for almost any messaging scheme - in Python you send strings, and you can use `len()` to determine its length (even if it has embedded \0 characters). It’s mostly the receiving code that gets more complex.

The easiest enhancement is to make the first character of the message an indicator of message type, and have the type determine the length. Now you have two recvs - the first to get (at least) that first character so you can look up the length, and the second in a loop to get the rest. If you decide to go the delimited route, you’ll be receiving in some arbitrary chunk size, (4096 or 8192 is frequently a good match for network buffer sizes), and scanning what you’ve received for a delimiter.

One complication to be aware of: if your conversational protocol allows multiple messages to be sent back to back (without some kind of reply), and you pass `recv()^ an arbitrary chunk size, you may end up reading the start of a following message. You’ll need to put that aside and hold onto it, until it’s needed.

Prefixing the message with it’s length (say, as 5 numeric characters) gets more complex, because (believe it or not), you may not get all 5 characters in one recv. In playing around, you’ll get away with it; but in high network loads, your code will very quickly break unless you use two recv loops - the first to determine the length, the second to get the data part of the message. Nasty. This is also when you’ll discover that send does not always manage to get rid of everything in one pass. And despite having read this, you will eventually get bit by it!

We will discuss the issue of framming(delimiting messages) in later chapter: *Network data and Network errors*
