## Installing virtualenv in Erle


This is the [oficial website of virtualenv](https://virtualenv.pypa.io/en/latest/virtualenv.html#installation), where you can find infromation about the installation and the usage.

If you are connected to the Internet from Erle(by using a wireless nado usb) then you only need to type:
```
root@erlerobot:~#  pip install virtualenv
```

If not the process must be a bit more tedious:

First of all yu need to download the virtualenv
from [here](http://pypi.python.org/pypi/virtualenv)
Download the file called `virtualenv-1.11.6.tar.gz (md5, pgp)	`to your Pc. Then copy it to Erleboar, you can find in [this tutorial](http://erlerobotics.gitbooks.io/erle_gitbook_unixintroduction/annex_iii_network_connection_with_erle/README.html) how to do it.
Once you have copied it, type :

```virtualenv-1.11.6.tar.gz
root@erlerobot:~#  tar xvfz virtualenv-1.11.6.tar.gz
root@erlerobot:~# cd virtualenv-1.11.6
root@erlerobot:~# python setup.py install
 ```

 Congratulations you are now ready to use it!

