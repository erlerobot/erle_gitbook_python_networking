## Threading and Multi-processing

The essential idea of a threaded or multi-process server is that we take the simple and straightforward
server that we started out with (the `server_simple.py`) and run several copies of it at once so that
we can serve several clients at once, without making them wait on each other.

Using multiple threads or processes is very common, especially in high-capacity web and database
servers.In the Standard Library you can find the [ multiprocessing module](https://docs.python.org/2/library/multiprocessing.html).

(Note:The main program logic does not
even know which solution is being used; the two classes have a similar enough interface that either
Thread or Process can here be used interchangeably.)

Look the example at `server_multi.py`:
```python


import sys, time, lancelot
from multiprocessing import Process
from server_simple import server_loop
from threading import Thread

WORKER_CLASSES = {'thread': Thread, 'process': Process}
WORKER_MAX = 10

def start_worker(Worker, listen_sock):
    worker = Worker(target=server_loop, args=(listen_sock,))
    worker.daemon = True  # exit when the main process does
    worker.start()
    return worker

if __name__ == '__main__':
    if len(sys.argv) != 3 or sys.argv[2] not in WORKER_CLASSES:
        print >>sys.stderr, 'usage: server_multi.py interface thread|process'
        sys.exit(2)
    Worker = WORKER_CLASSES[sys.argv.pop()]  # setup() wants len(argv)==2

    # Every worker will accept() forever on the same listening socket.

    listen_sock = lancelot.setup()
    workers = []
    for i in range(WORKER_MAX):
        workers.append(start_worker(Worker, listen_sock))

    # Check every two seconds for dead workers, and replace them.

    while True:
        time.sleep(2)
        for worker in workers:
            if not worker.is_alive():
                print worker.name, "died; starting replacement worker"
                workers.remove(worker)
                workers.append(start_worker(Worker, listen_sock))
                ```

As you can see it is letting multiple threads or
processes all call `accept()` on the very same server socket, and instead of raising an error and insisting
that only one thread at a time be able to wait for an incoming connection, the operating system patiently
queues up all of our waiting workers and then wakes up one worker for each new connection that
arrives. The fact that a listening socket can be shared at all between threads and processes, and that the
operating system does round-robin balancing among the workers that are waiting on an `accept()` call, is
one of the great glories of the POSIX network stack and execution model; it makes programs like this very
simple to write.

