# Motivation

When it comes to microservices communication, there is a lot of boilerplate we need to write:
define data transfer objects (DTOs), write their JSON instances, write server, write client
library, and also maintain backwards compatibility when the contract changes.
I'm looking for a solution that would allow me to describe services in a declarative way and
then generate the server and client code based on that spec. Whenever the spec changes,
new code is generated and distributed to users, therefore I only need to maintain
one thing: the spec file.

# haskell-thrift-todo-backend

In this repo I'm going to use Apache Thrift to spec the todo-backend service, and then
use the Haskell code generator Thrift provides to generate server and client libraries.

## Apache Thrift

> Thrift is a _RPC framework_ that offers: an interface definition language (IDL) to describe
> services with; multiple transport options (sockets, pipes, HTTP, etc); and protocols
> (binary, JSON, compressed, and more). http://thrift.apache.org/static/files/thrift-20070401.pdf

### Install thrift

If you're using MacOS and brew, all you need to do is:

```
brew install thrift
```

otherwise follow the installation instructions at https://thrift.apache.org

### .thrift file

This file is the interface definition, made up of thrift types and Services, for
our todo-backend service.

see todo-backend.thrift

### thrift code generator

Once we have the thrift code generator installed and .thrift file defined we can use
the thrift compiler to generate the source code which will be used by the server
and client code we'll write.

To generate Haskell code:

```
thrift -r --gen hs --out lib/todo-backend/src/ todobackend.thrift
```

## Examples

### binary protocol and socket transport

see https://github.com/admanaut/haskell-thrift-todo-backend/tree/master/src/Binary

### JSON protocol and socket transport

see https://github.com/admanaut/haskell-thrift-todo-backend/tree/master/src/JSON

### JSON protocol and HTTP transport

~Unfortunately there's no implementation for HTTP transport in Haskell.~

It's relatively trivial to implement a memory transport that just buffer bytes
in memory. You can then plug that into an HTTP server and it works. _But_, you
don't get the (nice?) threaded server the Thrift library provides. That
shouldn't be a problem as long as the HTTP server is equally performant. Any
server implementation backed by `wai` can be considered high performant at
this point. The nice benefit of not buying into a Thrift server (even if there
were one available) is that you can deal with the request and response however
you see fit. That's especially important for headers, in case you want to
forward additional metadata to the server.

see https://github.com/admanaut/haskell-thrift-todo-backend/tree/master/src/Http

### execute examples

use stack to compile and execute the code

```
stack build
```

```
stack exec haskell-thrift-todo-backend
```
