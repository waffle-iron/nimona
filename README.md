# Nimona

Nimona is a collection of specifications and reference implementations that
will provide a base for building decentralized peer to peer networks.

## 1. Network ([Fabric](fabric.md))

Fabric is a networking library that provides some very opinionated features
targeting mainly peer to peer and decentralized systems.

A custom network address notations that visibly denotes the tranport and 
service protocol, as well as any middleware that are needed to connect to a 
peer.  
A number of middleware will be supported out of the box, such as transport 
layer security, stream multiplexing, relaying through proxy peers, identity 
verification, and others.

## 2. Identity

An identity layer on top of the network allows users to use a single identity
in multiple p2p applications.

Fabric's discovery protocol will allow finding peers for specific users 
(identities), or even the peers that support a specific protocol.

## 3. [Graph based conflict resolution data type](graph-crdt.md)

One of the last pieces of this p2p puzzle is how applications deal with data.  
This includes storage, synchronation, conflict resolution, permissions, 
authenticity, etc.

A custom Conflig Resolution Data Type (CRDT) allows applications to use a 
set of pre-defined event nodes to create permissioned graphs (DAGs to be 
more specific) that can be shared and kept in sync with other users on the 
network.