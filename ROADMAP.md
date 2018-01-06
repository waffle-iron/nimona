# Roadmap

## v0.1.0

Creating a base network.

### Outline

* Addressing specification
* Protocol negotiation specification
* Relaying specification
* Fabric network
  * TCP transport with UPNP support
  * Protocol negotiation with encryption, relaying, and multiplexing support

### Tasks

* [nimona] Add addressing specification
* [nimona] Add protocol/stream negotiation specification
* [nimona] Add relaying specification
* [fabric] Add functionality for fabric to return all the addresses that its transports are listening on
* [fabric] Add functionality for fabric to return all protocols it supports
* [fabric] Add UPNP support to TCP transport
* [fabric] Add functionality for the TCP transport to also include the public ip to the addresses it is listening on, if upnp was successful
* [fabric] Add relaying middleware
* [fabric] Add tls encryption middleware
* [fabric] Add multiplexing middlware on top of yamux

## v0.2.0

Creating a trusted network.

### Outline

* Identity and peer specification
* Fabric network
  * Resolvers for figuring out how to connect to peers
  * Basic peer registry
  * Identity exchange middleware

### Tasks

* [nimona] Add identity and peer specification
  - Can one identity exist in more than one peers? Could different peers support different protocols? How do we do discovery and routing?
* [nimona] Add registry specification and API
* [fabric] Add peer resolution functionality with runtime added resolvers
* [registry] Add basic peer registry store
* [fabric] Add peer info structure
* [fabric] Add functionality for peer info to sign and verify signatures
* [fabric] Add resolver that uses the registery to find addresses for peers we are trying to dial
* [fabric] Add functionality to registry resolver to register all transport addresses for our peer
* [fabric] Add identity exchange middleware that makes sure the peer on the other side is the one we were connecting to
* ...

## v0.3.0

Creating a decentralized network.

### Outline

* DHT peer, protocol discovery and key value store specification
* Fabric network
  * DHT based key-value store
  * DHT based resolver for peers

### Tasks

* [nimona] Add DHT resolver and key value store specification
* [kaddht] Add DHT key value store and peer discovery
* [fabric] Add resolver that uses the DHT peer discovery to find addresses for peers we are trying to dial
* [fabric] Add functionality to DHT resolver to register all transport addresses for our peer
* ...

## v0.4.0

Distributing data on the network.

### Outline

* Hashgraph based CRDT specification
* Wire and synchronization specification
* Oak hashgraphs
  * Wire and synchronization over fabric
  * Registry and persistence

### Tasks

* [nimona] Add hashgraph based CRDT specification
* [nimona] Add wire and synchronization specification
* [oak] Add CRDT structures and methods for creating, verifying, and traversing the graph
* [oak] Add hashgrah registry for storing and retrieving all known hashgraphs
* [oak] Add disk persistence for hashgraph registry
* [oak] Add wire & sync handler for fabric
* ...

## 0.x.x

Bonus or would be nice to have at any point.

### Tasks

* [fabric] Add WebRTC data channels transport
* [fabric] Add UDP transport
* [fabric] Add WS transport
* [fabric] Add JS implementation on WS or WebRTC