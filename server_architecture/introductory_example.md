## Introductory example

In this minimalist protocol `lancelot.py`, the
client opens a socket, sends across one of the three questions asked of Sir Launcelot at the Bridge of
Death in Monty Python’s Holy Grail movie, and then terminates the message with a question mark:
`What is your name?`
The server replies by sending back the appropriate answer, which always ends with a period:
`My name is Sir Launcelot of Camelot.`
Both question and answer are encoded as ASCII.
```python
import socket, sys

PORT = 1060
qa = (('What is your name?', 'My name is Sir Lancelot of Camelot.'),
      ('What is your quest?', 'To seek the Holy Grail.'),
      ('What is your favorite color?', 'Blue.'))
qadict = dict(qa)

def recv_until(sock, suffix):
    message = ''
    while not message.endswith(suffix):
        data = sock.recv(4096)
        if not data:
            raise EOFError('socket closed before we saw %r' % suffix)
        message += data
    return message

def setup():
    if len(sys.argv) != 2:
        print >>sys.stderr, 'usage: %s interface' % sys.argv[0]
        exit(2)
    interface = sys.argv[1]
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    sock.bind((interface, PORT))
    sock.listen(128)
    print 'Ready and listening at %r port %d' % (interface, PORT)
    return sock
    ```
The server code is `server_simple.py`:
```python

import lancelot

def handle_client(client_sock):
    try:
        while True:
            question = lancelot.recv_until(client_sock, '?')
            answer = lancelot.qadict[question]
            client_sock.sendall(answer)
    except EOFError:
        client_sock.close()

def server_loop(listen_sock):
    while True:
        client_sock, sockname = listen_sock.accept()
        handle_client(client_sock)

if __name__ == '__main__':
    listen_sock = lancelot.setup()
    server_loop(listen_sock)
    ```

Anyway, this simple server has terrible performance characteristics.
The difficulty comes when many clients all want to connect at
the same time. The first client’s socket will be returned by `accept()`, and the server will enter the
`handle_client()` loop to start answering that first client’s questions. But while the questions and
answers are trundling back and forth across the network, all of the other clients are forced to queue up.
