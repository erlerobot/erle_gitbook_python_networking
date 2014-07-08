## Deadlock

The term “deadlock” is used for all sorts of situations in computer science where two programs, sharing
limited resources, can wind up waiting on each other forever because of poor planning. It turns out that
it can happen fairly easily when using TCP.


Take a look at`tcp_deadlock.py` for an example of a server and client that try to be a bit too clever without
thinking through the consequences. Here, the server author has done something that is actually quite
intelligent. His job is to turn an arbitrary amount of text into uppercase. Recognizing that its client’s
requests can be arbitrarily large, and that one could run out of memory trying to read an entire stream of
input before trying to process it, the server reads and processes small blocks of 1,024 bytes at a time.
```python

import socket, sys
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

HOST = '127.0.0.1'
PORT = 1060

if sys.argv[1:] == ['server']:
    s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    s.bind((HOST, PORT))
    s.listen(1)
    while True:
        print 'Listening at', s.getsockname()
        sc, sockname = s.accept()
        print 'Processing up to 1024 bytes at a time from', sockname
        n = 0
        while True:
            message = sc.recv(1024)
            if not message:
                break
            sc.sendall(message.upper())  # send it back uppercase
            n += len(message)
            print '\r%d bytes processed so far' % (n,),
            sys.stdout.flush()
        print
        sc.close()
        print 'Completed processing'

elif len(sys.argv) == 3 and sys.argv[1] == 'client' and sys.argv[2].isdigit():

    bytes = (int(sys.argv[2]) + 15) // 16 * 16  # round up to // 16
    message = 'capitalize this!'  # 16-byte message to repeat over and over

    print 'Sending', bytes, 'bytes of data, in chunks of 16 bytes'
    s.connect((HOST, PORT))

    sent = 0
    while sent < bytes:
        s.sendall(message)
        sent += len(message)
        print '\r%d bytes sent' % (sent,),
        sys.stdout.flush()

    print
    s.shutdown(socket.SHUT_WR)

    print 'Receiving all the data the server sends back'

    received = 0
    while True:
        data = s.recv(42)
        if not received:
            print 'The first data received says', repr(data)
        received += len(data)
        if not data:
            break
        print '\r%d bytes received' % (received,),

    s.close()

else:
    print >>sys.stderr, 'usage: tcp_deadlock.py server | client <bytes>'
    ```

If you start the
server and then run the client with a command-line argument specifying a modest number of bytes—
say, asking it to send 32 bytes of data (for simplicity, it will round whatever value you supply up to a
multiple of 16 bytes)—then it will get its text back in all uppercase:
```
root@erlerobot:~/Python_files#  python tcp_deadlock.py server
Listening at ('127.0.0.1', 1060)
```

```
root@erlerobot:~/Python_files# python tcp_deadlock.py client 32
Sending 32 bytes of data, in chunks of 16 bytes
32 bytes sent
Receiving all the data the server sends back
The first data received says 'CAPITALIZE THIS!CAPITALIZE THIS!'
32 bytes received
```
On the server screen this is displayed:
```
Processing up to 1024 bytes at a time from ('127.0.0.1', 49702)
32 bytes processed so far
Completed processing
Listening at ('127.0.0.1', 1060)
```
Now,try using the client to send a very large
stream of data, say, one totaling a gigabyte:
```
root@erlerobot:~/Python_files# python tcp_deadlock.py client 1073741824
Sending 1073741824 bytes of data, in chunks of 16 bytes
1399600 bytes sent
```
In the server window:
```
Processing up to 1024 bytes at a time from ('127.0.0.1', 49703)
688032 bytes processed so far

```
You will see both the client and the server furiously updating their terminal windows as they
breathlessly update you with the amount of data they have transmitted and received. The numbers will
climb and climb until, quite suddenly, both connections freeze.The server’s output buffer and the client’s input buffer have both finally filled, and
TCP has used its window adjustment protocol to signal this fact and stop the socket from sending more
data that would have to be discarded and later re-sent.
