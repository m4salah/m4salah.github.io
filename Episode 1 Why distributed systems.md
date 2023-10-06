![](/public/47b6b9ef441398e9f9e31783026fb95783abefe2c063d71d03faf90341f96069.webp)

#distributedsystems

# [Why distribute systems?](https://www.youtube.com/watch?v=s_p3I5CMGJw)
## The Wrong reasons:

- It's fun
- My boss tells me to make it distributed systems/microservices
- make it distributed without experience because we worry of future scalability.

## The right reasons:

- Scalability of course (balance the load across machines)
- Reduce latency for users around the globe, and serve the request as fast as possible this requires us to make the server as close as we can to the user (better user experience), but we are still bounded by the light speed theoretically [Latency Numbers Every Programmer Should Know](https://gist.github.com/jboner/2841832).
- Availability, fault tolerance (under failure), Durability.
- Data residency under compliance with country law (for example the country requires the data of some sort to be physically in the country).


# Node: 
The node that contains the compute (CPU) and state (memory, disk), it's either a standalone machine or a running process (a member of the the distributed systems).
# Compute:
The computing power that does the actual work is The ***CPU***.
# State:
Somewhere holds data memory, disk, or database.
# Distributed systems:
The process is by which we split the compute (CPU) the state (memory, disk), or both (Node) into multiple machines and they are communicating asynchronously through unreliable communication channels (Can fail).

## Stateless distributed system:
***the state is offloaded.***
Which machine doesn't hold the state, the state persists elsewhere and accesses it. for example, web applications run on many servers but they access the same database or database cluster. 
![](/public/e322301775b0d61312d5bbb284985872489abe63c4b1254013f93ea57b77faf6.png)

## Stateful distributed system:
***Every Node holds a state***
Which machine holds the state of its own. for example 
**If we have Node A and Node B:**
-  if State A = State B then State A and State B are ***Replicas***, 
	1. if they are the same at any point in time. we call that ***Strong Consistency***. 
	2. if they are *eventually* will be the same we call that ***Eventual Constituency***
- if the State = State A + State B we call that ***Sharding or Partitioning***

![](/public/0c8904c8b92b1513ad4002c4aa8847b34ca6bc35c931e2bcde13cf4d956b94f4.png)
With this mechanism, we trade off the availability because if one machine is down we do not have the whole state. to mitigate this problem how likely is the company can pay for higher availability? like replicating the machine into whatever we want.


# Fault tolerance

## Node Failures
- Fail stop (hard failure): the node is either running or not stopped.
- Gray failure (byzantine failure): the node is running but it is behaving. for example, the node is very slow, or sends wrong responses, or corrupted results, or has an old version of something. This is the hardest part of the distributed systems is to be resilient against the byzantine failure.

## Network Failures
- Fail stop (hard failure).
- Gray failure (byzantine failure).
### Network partitioning problem
Suppose we have 6 nodes that are connected through the network, the partitioning problem happens when there is a subset of nodes separated from the other nodes and they think that they are the only nodes alive and the same for the other nodes.

![](/public/935fec2d7b986dc84b7793b6be63db345c414747700122d4ef0f802ecf236072.png)

## CAP theorem
C for Consistency, A for Availability, and P for Partitioning tolerance. 

## Do we have to be fault-tolerant against all failures
Of course not and depending on the use case, the key here is what to trade for ***Engineering is the Art of making tradeoffs*** and how to communicate that to business owners.
**It's more important to solve how to recover from the failure not to be fault tolerant against (recoverable)**

## Two generals problem
Suppose we have two armies (army 1 and army 2) that need to invade the country but they must attack together at the same time to invade the country.
they must be communicating to agree on the time to invade, we suppose the messenger is authentic and the message will be received is **authentic and true**.
If we send the messenger from Army 1 with a certain time to invade and Army 2 receives the message and agrees on the time he sends another messenger to confirm the invasion time.
The problem here is from Army 1 point he sends the messenger and assumes the Army 2 received it and will attack on time or he waits for the response from Army 2 to confirm but from Army 2 point he confirms the time and sends the messenger but he didn't know if army 1 received the confirmation or not, Do we make army 1 send another messenger to confirm the confirmation, we are stuck in a loop, it requires send and received an infinite number of messages to confirm the time, This type problem called ***Consensus (common knowledge)***. to reach the consensus we require an infinite number of messages (assurance problem). ***No Solution for this problem for 100%***.
![](/public/41729c8ecc504d2514214c12c3b5425efb43546418ecbe5c9ba7e084910dc3c0.png)
## Byzantine Generals problem
The same as the previous problem but the messages is will be received 100% we assure you that the message will be received but the message maybe it's corrupted or the messenger is disloyal or one of the army is actively sending wrong information (gray failure the army or the node is running but actively send wrong messages or corrupted or compromised or the messenger late).

## Consensus Problem
In distributed systems each node has a state, We must agree that all the node has equal state (Consensus) in unreliable communication channels between the nodes.
There are a class of algorithms that specialize in solving consensus problems for example Paxos, and Raft

