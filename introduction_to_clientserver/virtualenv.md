## Virtualenv
A common situation is that you find a *Python package* that sounds like it might already
do exactly what you want, and that you want to try it out on your system. For this you should be introduce to very best Python technology for quickly trying out a new library:**virtualenv**

In the old days, installing a Python package was a gruesome and irreversible act that required
administrative privileges on your machine and left your system Python install permanently altered.

Careful Python programmers do not suffer from this situation any longer. Many of them install only
one Python package system-wide: virtualenv. Once virtualenv is installed, you have the power to create
any number of small, self-contained “virtual Python environments” where packages can be installed,
un-installed, and experimented with without contaminating your system-wide Python. When a
particular project or experiment is over, you simply remove its virtual environment directory, and your
system is clean.

