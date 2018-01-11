# Fabric

Fabric is a networking library that provides some very opinionated features
targeting mainly peer to peer and decentralized systems.  

- Verbose network address notations that expose tranports, protocols, 
  middleware, etc
- Protocols multiplexing and negotiation over the same transport layer
- Optional peer and service discovery
- Optional routing connections through proxy peers

That being said, there is nothing prohibiting its use in other applications, 
eg microservices.  

## Problem statement

In peer to peer networks each peer's network needs and requirements are more 
or less unique.  
Peers want or have to use different transport layers, different protocols,
different encoding schemes etc depending on their platform, use case, and
device and hardware or network capabilities.  

Different peers have different ways of being accessed. 
This usually ends up requiring both the clients and servers to have complex 
logic for handling connections, routing, etc or forcing the protocol's 
designers to specify them in advance.

## Features

### Addresses

Fabric's addresses are a series of `/` delimited transport and stream 
protocols that the client and server need to go through until connection 
is established.

Each part contains the protocol `:` delimeted parameters, but the left-most
item will need to always be the protocol or stream's identifier.

For example: `tcp:172.16.0.1:3000/tls/http`

### Dialing

In the previous example, the first part, `tcp:172.16.0.1:3000` tells the 
client that it needs to connect via the `tcp` protocol. 
The rest (`172.16.0.1:3000`) will be provided to the procotol dialer so it 
knows what it needs to do.

The `tcp` protocol dialer will the connect to the ip `172.16.0.1` port `3000`.
Once the connection is established, the dialer will give up the stream to the
`tls` protocol, once that completes its job, will give the stream up to `http`.

### Stream multiplexing && protocol selection

Peer to peer networks are very complex, and usually most of their peers are
behind NATs, firewalls, and other things that limit incoming connections from
the internet, or require opening ports, either manually or through upnp.

By allowing multiple streams to go through the same connection, we can have
multiple protocols listening on the same port, and provide them with security,
authentication, and other things that each protocol would have to implement
on its own.

This means that the client will need to negotiate with the server about the
protocol it wishes to use.

Let's assume that we have a server that supports two dummy protocols, `ping`
and `pong` both just over `tcp` on port `8000`.

The addresses for these two would then be:

* `tcp:172.16.0.1:8000/fabric:select/ping`
* `tcp:172.16.0.1:8000/fabric:select/pong`

They are both on the same transport protocol, ip, and port.  
Once the `tcp` connection is established, it would be passed to the `fabric`
middleware, which would then negotiate either the `ping` or `pong` protocols.

### Discovery && Resolution

Assuming that when we want to connec for the first time to a peer, we'll only
know the peer's id.
The address that `peer A` would need in order to connect to `peer B` for the
`ping` protocol in its simplest form would be something like `peer:B/ping`.

`Peer B` will at some point try to dial `peer:B/ping`.  

Since Fabric won't have a transport for `peer:B` it will try to resolve this
using a number of resolvers. (Initially a registry and a Kademlia DHT resolver
will be available but more can be added in the future).

The DHT resolver will find a couple of addresses that `peer B` has advertised
for itself:

* `tcp:172.16.0.1:3000/tls/yamux`
* `ws:172.16.0.1:3001/tls`
* `peer:C/tls/yamux/relay:peer:B/tls/yamux`
* `tcp:172.16.0.2:3080/tls/yamux/fabric:select`

The addresses show exactly what needs to happen, to these addresses the 
original address will appened so they'll look something like:

* `tcp:172.16.0.1:3000/tls/yamux/ping`
* `ws:172.16.0.1:3001/tls/yamux/ping`
* `peer:C/tls/yamux/relay:peer:B/tls/yamux/ping`
* `tcp:172.16.0.2:3080/tls/yamux/fabric:select`

In the first two, connect to a peer with either TCP (`tcp`) or WebSockets 
(`ws`), negotiate TLS (`tls`), negotiate the yamux multiplexer (`yamux`) so we
can open new bi-directional connections on the same socket, make sure we are 
talking to the corrrect peer (`peer`), and finaly negotiate the protocol we
need to reach (`ping`).

The third address has a bit more information but the gist is the same. It will
connect to a third, relay peer, do some stuff, and eventually create a relay
to the original target. This will allow peer B to be accessible even if it is 
not publicly accessible or properly NATed.

The last one is a `tcp` connection with `tls`, `yamux`, and `fabric:select`
to allow the server to handle multiple protocols on the sameport.

This way we can build a number of "middleware" protocols that will wrap the 
primary connection as well as add contextual information to it.

### Other middlware/protocols

A `handshake` middleware could for example performa handshare between the two
peers that will allow them to learn and verify the identity of the other part
and add this information to the connection on the local and remote peers so 
other middleware can use this information, or add their own.

This is useful in cases where one server is for example hosting more than one
peers and the middleware down the chain need to know which peer this connection
is for.

### Address examples

* Simple connections: `tcp:127.0.0.1:3000/http`, `tcp:127.0.0.1:3001/hello`
* Connections with protocol selection: `tcp:127.0.0.1:3000/fabric:select/ping`, `tcp:127.0.0.1:3001/fabric:select/pong`
* Connections with identity negotiation/selection: `tcp:127.0.0.1:3000/fabric:select/fabcric:handshake:4D309D0C/hello`
* Multiplexing connections: `tcp:127.0.0.1:3000/yamux/fabric:select/hello`
* Relaying connections: `tcp:127.0.0.1:3000/yamux/fabric:select/fabric:relay:4D309D0C/hello`
* Other transports: `wss:foo.bar.com/hello`
