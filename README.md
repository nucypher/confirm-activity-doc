# Confirm Activity 

This repository contains proposed solutions for the confirm activity problem in the NuCypher network. This problem is concerned with how to prove that an Ursula is indeed active and providing service in the network. 

The presented work considers two definitions of the problem: confirm availability and confirm service activity. The former is about confirming that each Ursula is indeed online and willing to serve Bob requests, while the latter is about confirming that Ursula served a distinct number of requests withing a given period. Such definitions arise due to the difference in requirements of the different stages of the network operation. During the initial stage, while subsidies still exist, confirm availability is what we are after to increase adoption. At a later stage, when the only income source is service fees, we think that confirm service activity is what we need.

## Repo Organization
confirm-activity/ contains the full research work on the confirm activity problem, while CESC-submission/ contains the paper submitted to CESC 2020.

## Current Work
Several solutions are proposed that vary based on the problem they solve, threat model, and efficiency. The first one is for confirm availability, while the rest are for confirm service activity.

### Optimistic Challenge-based Approach
Here Bob request the service as usual. If the working Ursula is not responding Bob falls back to a challenge protocol to investigate this Ursula and punish her financially if unavailability is confirmed. At a high level, for each round a set of Ursulas, called gateways, is elected. Bob complains to these gateways, which in turn forward Bob's request to the working Ursula and wait for an answer. If not reply is received, the gateways sign a statement attesting that the working Ursula is not responsive. This will result in slashing part of the working Ursula stake as a punishment.

### ZKP-based Scheme
we utilize the fact that in our network each Ursula already produces a ZKP attesting to the correctness of each re-encryption request she performs. Our solution is to aggregate the proofs of all served requests within a round into one proof attesting to the above statement. This accumulation can be done by using generic techniques like proof carrying data (PCD) or incrementally verifiable computation (IVC). An alternative, possibly more efficient approach, is to view the statement that an Ursula served a given number of requests as an instance of arithmetic circuit satisfiability problem and combine it with a highly efficient ZKP system, e.g., Bulletproofs, to produce a proof attesting to the statement claim. 

### Committee-Attestation Scheme
This solution shares a similar theme to confirm availability solution mentioned previously. For each round, a committee of Ursulas (and possibly outsider verifiers) is elected to attest for the work load an Ursula served. In detail, the working Ursula sends all Bob requests she served along with the correctness proofs to this committee. After verifying all the proofs, the committee collectively signs a statement attesting to the workload performed by Ursula. The committee (or the working Ursula) submits the signed statement to the network so the working Ursula can claim her payments. 

### Commit/Challenge/Open-based Scheme
Here we introduce a mechanism inspired from the Merkle tree-based ZKP system. For each round a working Ursula constructs a Merkle hash tree of all served requests and their correctness proofs. Then, she uses the root as a random beacon to be fed into an iterative hash process to select n leaf nodes at random to open them (i.e., these are the challenges). Ursula signs the Merkle tree root and the activity proof will be composed of the signed root, the challenge leaf nodes along with their openings, and membership paths of the challenge leaves. Ursula sends this proof either to a sidechain (maintained by round-selected committee) or to the main network to verify. Once verified, Ursula will be rewarded with the service payment.

## Future Work
* Implement the optimistic challenge-based solution into the network.
* Collect data from the network once we move to phase two (when the inflation rewards disappear) and assess the need to switch to any of the confirm service activity solutions.
* Implement the three solutions of the confirm service activity and compare their performance to choose the best (if needed in the network).