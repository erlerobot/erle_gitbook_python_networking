## Non - blocking sockets


In Python, you use `socket.setblocking(0)` to make it non-blocking. You do this after creating the socket, but before using it. (Actually, if you’re nuts, you can switch back and forth.)

The major mechanical difference is that `send()`, `recv()`, connect and accept can return without having done anything. You have (of course) a number of choices. You can check return code and error codes and generally drive yourself crazy. Your app will grow large, buggy and suck CPU. So let’s skip the brain-dead solutions and do it right.Use select.


```python
ready_to_read, ready_to_write, in_error = \
               select.select(
                  potential_readers,
                  potential_writers,
                  potential_errs,
                  timeout)
                  ````

You pass select three lists: the first contains all sockets that you might want to try reading; the second all the sockets you might want to try writing to, and the last (normally left empty) those that you want to check for errors. You should note that a socket can go into more than one list. The select call is blocking, but you can give it a timeout. This is generally a sensible thing to do - give it a nice long timeout (say a minute) unless you have good reason to do otherwise.

In return, you will get three lists. They contain the sockets that are actually readable, writable and in error. Each of these lists is a subset (possibly empty) of the corresponding list you passed in.

If a socket is in the output readable list, you can be as-close-to-certain-as-we-ever-get-in-this-business that a recv on that socket will return something. Same idea for the writable list. You’ll be able to send something. Maybe not all you want to, but something is better than nothing. (Actually, any reasonably healthy socket will return as writable - it just means outbound network buffer space is available.)

If you have a “server” socket, put it in the `potential_readers` list. If it comes out in the readable list, your accept will (almost certainly) work. If you have created a new socket to connect to someone else, put it in the `potential_writers` list. If it shows up in the writable list, you have a decent chance that it has connected.

One very nasty problem with select: if somewhere in those input lists of sockets is one which has died a nasty death, the select will fail. You then need to loop through every single damn socket in all those lists and do a `select([sock],[],[],0)` until you find the bad one. That timeout of 0 means it won’t take long, but it’s ugly.

Actually, select can be handy even with blocking sockets. It’s one way of determining whether you will block - the socket returns as readable when there’s something in the buffers. However, this still doesn’t help with the problem of determining whether the other end is done, or just busy with something else.

Portability alert: On Unix, select works both with the sockets and files. Don’t try this on Windows. On Windows, select works with sockets only.
