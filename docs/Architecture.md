# Skinny Architecture
This document describes the overall architecture of Skinny. It does *not* describe the specific protocol or provide proofs of the systems correctness.

## Data Storing
The Skinny network works by storing a hash of a entries key along with a hash of the owners signature (the cryptographic public key), a hash of the stored data and an expiration time. Additionally individual members of the Skinny network will record that they have a copy of the individual data (note that this means that less members of the network have to have the data compared to the metadata).

Paxos master election weighted by individual nodes voting rights is used to accept changes to the keys. A well behaving node should only accept an update if the signature of the new update matches the stored signature + the ttl is less than the maximum ttl it will accept. If no key is present then the stored signature check is ignored.

## Network Membership
The list of network members is also stored in the skinny network with the server address as the key, the public key hash stored as before, the ttl represents when the interned bitcoins will expire. In this there is associated inline data (the amount of interned bitcoin) stored in the hash field. An update of this special form is processed according to the normal paxos master election process with the additional data verification that an acceptor will not accept an update if the amount of bitcoin internment does not match the inline data.

Members are removed when a full vote of the network removes them. There is no expiration process for these keys. Nodes should make sure to updte their keys regularly, otherwise other nodes may propose to remove them if they have not been seen online for a period of time.

## Clients Retrieving Data
In order to retrieve data clients should go to as many network nodes as they desire for security purposes and find the data for the key they wish to retrieve. They should go to the associated server where the data is stored and download it, making sure that the data retrieved matches the hash in the network.

## Clients Storing Data
A client which wishes to store data on the network must convince at least one node on the network to store their data (ideally they would choose more than 1 for replication purposes). These nodes may be paid by the client. The nodes will accept the data and broadcast the metadata to the network. Note that they should *not* have the private key which should rest with the client.
