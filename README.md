# Confirm Activity 

This repository contains proposed solutions for the confirm activity problem in the NuCypher network. That is, how to prove that an Ursula is indeed active and providing service in the network. This is a response to the current approach adopted in the network, in which an Ursula simply calls a function periodically to confirm she is online. Such an approach does not guarantee that Ursula is well behaving as it can be offline (not serving any request) and just go online to call this function when needed.

This work considers two definitions of the problem: confirm availability and confirm service activity. The former is about confirming that each Ursula is indeed online and willing to serve Bob requests, while the latter is about confirming that Ursula served a distinct number of requests withing a given period. Such definitions arise due to the difference in requirements of the different stages of the network operations. During the initial stage, while service fees still exist, confirm availability is what we are after to increase adoption. At a later stage, when the only income source is service fees, we think that confirm service activity is what we need.


## Current Work
Several solutions are proposed that vary based on the problem they solve, threat model, and efficiency. The first one is for confirm availability, while the rest are for confirm service activity.

### Optimistic Challenge-based Approach
Here Bob request the service as usual. If the working Ursula is not responding Bob falls back to a challenge protocol to investigate this Ursula and punish her financially if unavailability is confirmed. At a high level, for each round a set of Ursulas, called gateway Ursulas, are elected. Bob complains to these gateways, which in turn forward Bob's request to the working Ursula and wait for an answer. If not reply is received, the gateways sign a statement attesting that the working Ursula is not responsive. This will result in slashing part of the working Ursula stake as a punishment.

### PCD-based Scheme
This solution is still a high level without a concrete construction. The main idea is to utilize [proof carrying data](http://people.csail.mit.edu/alexch/research/pcd/pcd-ics.pdf) to build a primitive that allows Ursula to accumulate the correctness proofs of the re-encryption requests it serves into one proof that attest to the total number of distinct requests served within a period. Then the service fees from each policy will be distributed based on the number of requests that Alice served. 

### CoSi-Based Scheme
Here we utilize [collective signing](https://arxiv.org/pdf/1503.08768.pdf) to allow a committee of Ursulas to attest to the fact that a given Ursula has served a given number of distinct requests. In short, a working Ursula keeps a full record of all requests (and their correctness proofs) and send this record to the committee. After verifying the log, the committee will collectively sign a service activity proof. 

### Commit/Challenge/Open-based Scheme
Here simply Ursula will construct a Merkle tree of all the served requests within a round and publishes the root (signed) on the blockchain. The policy contract will then challenge Ursula to open a randomly selected set of leaves, and will pay Ursula her service fees only if it opens the challenged leaves correctly.

## Future Work
* Implement the optimistic challenge-based solution into the network.
* Collect data from the network once we move to phase two (when the inflation rewards disappear) and assess the need to switch to any of the confirm service activity solutions.
* Work on the PCD-based approach to come up with a practical concrete construction, if possible.
* Implement the three solutions of the confirm service activity and compare their performance to choose the best (if needed in the network).