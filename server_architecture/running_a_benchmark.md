## Running a Benchmark

We need a system for comparing the subsequent server designs that we explore.We are therefore going to turn now to a public tool: the [FunkLoad tool](https://pypi.python.org/pypi/funkload/1.16.1), written in Python and
available from the Python Package Index.
```
root@erlerobot:~/Python_files#  pip install funkload
```
FunkLoad can take a test routine and run more and more copies of it simultaneously to test how the
resources it needs struggle with the rising load. Our test routine`launcelot_tests.py` will be an expanded version of the
simple client that we used earlier: it will ask ten questions of the server instead of three, so that the
network conversation itself will take up more time relative to the TCP setup and teardown times that
come at the beginning and end.

```python

from funkload.FunkLoadTestCase import FunkLoadTestCase
import socket, os, unittest, lancelot

SERVER_HOST = os.environ.get('LAUNCELOT_SERVER', 'localhost')

class TestLancelot(FunkLoadTestCase):
    def test_dialog(self):
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.connect((SERVER_HOST, lancelot.PORT))
        for i in range(10):
            question, answer = lancelot.qa[i % len(launcelot.qa)]
            sock.sendall(question)
            reply = lancelot.recv_until(sock, '.')
            self.assertEqual(reply, answer)
        sock.close()

if __name__ == '__main__':
    unittest.main()
    ```

So here on my laptop, I run the server, giving it a blank interface name so that it will accept
connections on any network interface:
```
root@erlerobot:~/Python_files# python server_simple.py ''
```

On the other machine, I create a small FunkLoad configuration file that
arranges a rather aggressive test with an increasing number of test users all trying to make repeated
connections to the server at once—where a “user” simply runs, over and over again, the test case that
you name on the command line. Read the FunkLoad documentation for an explanation, accompanied
by nice ASCII-art diagrams, of what the various parameters mean.
```
# TestLauncelot.conf
[main]
title=Load Test For Chapter 7
description=From the Foundations of Python Network Programming
url=http://localhost:1060/

[ftest]
log_path = ftest.log
result_path = ftest.xml
sleep_time_min = 0
sleep_time_max = 0

[bench]
log_to = file
log_path = bench.log
result_path = bench.xml
cycles = 1:2:3:5:7:10:13:16:20
duration = 8
startup_delay = 0.1
sleep_time = 0.01
cycle_time = 10
sleep_time_min = 0
sleep_time_max = 0
``
