## Create a virtual environment to test  packages

We are now going to use virtualenv to create a new environment and intall the `googlemaps`package on it.
You can read more about this package [here](http://pypi.python.org/pypi/googlemaps/).


Now you type the following:
```
root@erlerobot:~# virtualenv --no-site-packages gmapenv
New python executable in gmapenv/bin/python
Installing setuptools, pip...done.
root@erlerobot:~#
root@erlerobot:~# cd gmapenv
root@erlerobot:~/gmapenv# ls
bin  include  lib  local
root@erlerobot:~/gmapenv# . bin/activate
(gmapenv)root@erlerobot:~/gmapenv# python -c 'import googlemaps'
Traceback (most recent call last):
  File "<string>", line 1, in <module>
ImportError: No module named googlemaps
(gmapenv)root@erlerobot:~/gmapenv#
```
As you can see, the googlemaps package is not yet available. To install it, use the pip command that is
inside your virtualenv and that is now on your path thanks to the activate command that you ran:
```
(gmapenv)root@erlerobot:~/gmapenv#  pip install googlemaps
Downloading/unpacking googlemaps
Downloading googlemaps-1.0.2.tar.gz (60Kb): 60Kb downloaded
Running setup.py egg_info for package googlemaps
Installing collected packages: googlemaps
Running setup.py install for googlemaps
Successfully installed googlemaps
Cleaning up...
```
The python binary inside the virtualenv will now have the googlemaps package available:
```
(gmapenv)root@erlerobot:~/gmapenv# python -c 'import googlemaps'
```

When you install a packet, you should be carefull: it must be suitable for Erle architecture.
