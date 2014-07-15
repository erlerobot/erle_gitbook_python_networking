## Elementary client

We will tackle the deficiencies of the simple server shown in `server_simple.py` in two discussions.
First, in this
section, we will discuss how much time it spends waiting even on one client that needs to ask several
questions; and in the next section, we will look at how it behaves when confronted with many clients at
once.
A simple client for the Launcelot protocol connects, asks each of the three
questions once, and then disconnects.
The code of `client.py`is the following:
```python

import socket, sys, lancelot

def client(hostname, port):
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((hostname, port))
    s.sendall(lancelot.qa[0][0])
    answer1 = lancelot.recv_until(s, '.')  # answers end with '.'
    s.sendall(lancelot.qa[1][0])
    answer2 = lancelot.recv_until(s, '.')
    s.sendall(lancelot.qa[2][0])
    answer3 = lancelot.recv_until(s, '.')
    s.close()
    print answer1
    print answer2
    print answer3

if __name__ == '__main__':
    if not 2 <= len(sys.argv) <= 3:
        print >>sys.stderr, 'usage: client.py hostname [port]'
        sys.exit(2)
    port = int(sys.argv[2]) if len(sys.argv) > 2 else lancelot.PORT
    client(sys.argv[1], port)
    ```
    With these two scripts in place, we can start running our server in one console window:
    ```
    root@erlerobot:~/Python_files#  python server_simple.py localhost
    Ready and listening at 'localhost' port 1060
    ```

We can then run our client in another window, and see the three answers returned by the server:

```
root@erlerobot:~/Python_files#  python client.py localhost
My name is Sir Lancelot of Camelot.
To seek the Holy Grail.
Blue.
```

The client and server run very quickly here on my laptop. But appearances are deceiving, so we had
better approach this client-server interaction more scientifically by bringing real measurements to bear
upon its activity.

The solution for measuring the real waiting time when running the client and server on a single machine, but to send the connection through a round-trip to another machine by way of an SSH tunnel.

When doing this you will notice how the cost of communication dominates the performance. It will always seem to take
less than 10 μs for the server to run the answer = line and retrieve the response that corresponds to a
particular question. If actually generating the answer were the client’s only job, then we could expect it
to serve more than 100,000 client requests per second
way of an SSH tunnel.
But look at all of the time that the client and server spend waiting for the network: every time one of
them finishes a `sendall()` call, it takes between 500 μs and 800 μs before the other conversation partner
is released from its `recv() call and can proceed.

Now on, we may need a system for comparing the subsequent server designs that we explore.We are therefore going to turn now to a public tool: the [FunkLoad tool](https://pypi.python.org/pypi/funkload/1.16.1), written in Python and
available from the Python Package Index.
```
root@erlerobot:~/Python_files#  pip install funkload
```
