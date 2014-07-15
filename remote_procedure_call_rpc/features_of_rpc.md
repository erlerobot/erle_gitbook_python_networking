## Features of RPC

Besides serving their the essential purpose of letting you make what appear to be local function or
method calls that are in fact passing across the network to a different server, RPC protocols have several
key features, and also some differences, that you should keep in mind when choosing and then
deploying an RPC client or server.

First, every RPC mechanism has limits on the kind of data you can pass.The most popular protocols, therefore, support only a few kinds of numbers and strings; one
sequence or list data type; and then something like a struct or associative array.

A second common feature is the ability of the server to signal that an exception occurred while it
was running the remote function. In such cases, the client RPC library will typically raise an exception
itself to tell the client that something has gone wrong.

Third, many RPC mechanisms provide introspection, which is a way for clients to list the calls that
are supported and perhaps to discover what arguments they take.

Fourth, each RPC mechanism needs to support some addressing scheme whereby you can reach
out and connect to a particular remote API. Some such mechanisms are quite complicated, and they
might even have the ability to automatically connect you to the correct server on your network for
performing a particular task, without your having to know its name beforehand. Other mechanisms are
quite simple and just ask you for the IP address, port number, or URL of the service you want to access.
These mechanisms expose the underlying network addressing scheme, rather than creating a scheme of
their own.

Finally, some RPC mechanisms support authentication, access control, and even full impersonation
of particular user accounts when RPC calls are made by several different client programs wielding
different credentials.
