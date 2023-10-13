![](/public/db7c50b33e4f2b5b748ab7c5abfe9732dca66170134996c751b436ea6cfed299.webp)

[Youtube video](https://www.youtube.com/watch?v=j8bLPfCJdSw)
#distributed #distributedsystems #statemachinereplication #paxos #consensus

We ended up the last episode with the definition of the ***Consensus Problem*** So we will try to come up with something to solve the Consensus problem.

Assume we have one machine with single-threaded single core or guaranteed as single core single-threaded (Node). It has state this state is *Hash Table* To change the state of this node I must make RPC to make an operation for example set(k1, v1), This is a network call so it will take some time to complete the operation, and return the response. There is a difference between the invocation time (make the call) and the completion time (The k1 is v1 now).

---
# Multiple calls on the node from different clients:
So I have two options if the two option
- ## Concurrent Execution 
	meaning there is **overlapping window** between the two clients call (join).
	![](/public/9398790a200c611e52ff3e14eea2f99f0a878aa8af77eb5fa3e3bdcd757dc1fc.png)
	*The green area is the overlapping window*
- ## Sequential Execution
	meaning there is no **overlapping window** between the two call (disjoint)
	![](/public/2ac4123c16886e13caeed9f0480b4f199adfc3c154cae6d23f7760ae225a8391.png)

---
# Design Key value store.
The design will be as follows.
We have a single machine held in a memory hash table, the interface to this store will be (get, set, contain, and remove) and you have to make a network call to store what you want.
![](/public/535ee4806e2163832e39d3e0b1bca242756f2e6085dc5cb9fe9dfba7dad97d97.png)

Consistency Guarantee from the node (single core single-threaded or it acts like single core single-threaded) is if c1 makes a call to set(k1, v1) and after that immediately c2 make a call get(k1) c2 should get v1 ***WHYYYY ?!***.
Because this is a single core single threaded he processes one call at a time so even if the call from c1 didn't finish setting the k1 c2 call will wait until the c1 call is processed until then there is already k1 -> v1 in the hash table.
![](/public/997c09eb8b8ff94c8fdd507e7e3c012aaba3dd3972bedd227b224cf99416c826.png)
Even though the two calls appear concurrently (overlapping), the actual behavior is ***Sequential Execution***. This property is called ***Strong Consistency*** 
## Strong Consistency
Is the property of a system that behaves from the outside like Single core single-threaded, The Execution happens as sequential order.
![](/public/185f2b0ef0fe788f64c001c74feb71cae6fe7a8351f1a0caba67ad8e1732207f.png)
In this image, this node is strongly consistent because the inside is concurrently but from the outside they happened sequentially.

## Scale this model to three nodes
So now we need to scale this to 3 nodes under those requirements 
1. Strong Consistency.
2. No Single point of failure (SPOF) or (fault tolerant). The system must be Stateful distributed, the **state is replicas** (No External State).

The model will be
![](/public/fda8f24cdc8ba935b927cdae8eaecfc90ead2d27d04b5e2c99f0def43b89ba0e.png)

So we will try to come up with a solution to make the three nodes agree on one state (consensus).
The first thing that will come into our mind is if any of the node gets a write it will tell the rest node what to change to to reach the same state, but what if c1 send set(k1, v1) to n1 and c2 send also in the same time set(k1, v1) to n2 we will reach in a weird state depending which call the other late or early there will be no consensus among the three nodes.
![](/public/050348292b5162613e778a25d41873577b3f439b5adec2927f78111b54b84eaf.png)

So will need what we call ***Total Order*** (Order of operation):
## Total Order (Order of operation):
We must agree on a way or some algorithm in which operation is the first and which is the second. Which operation I must execute first and so on.

---
Suppose we have a state for example counter and there is a set of commands for example increment and decrement like this.
![](/public/4c3e9d5da70346961650e84a294a08e63972f248a454f86b9d99539d1456c478.png)
The Register the counter or even the hash map is called **State Machine** because the command can change the state into another state, So What we are trying to solve is replicating the state among all nodes this is called ***State Machine Replication (SMR)***. Replicating the state (counter or register of hash map).

## Replicating the commands:
Assume we have n1 and n2 with the same initial state, running the same code (same algorithm) if they are executing the same command with the same order they definitely will land in the same state so I don't need to replicate the state I only need to replicate the command (same initial state, and same algorithm of course).
![](/public/7d513997a84b8fb78f04cff383c5aadaf827a782fbfb72aa867624676f64d76b.png)
We can replicate the Node under Three conditions:
1. Same Initial State
2. Same code (same algorithm)
3. Total Order of events(or commands)
## Add a new node to the cluster
With that knowledge it's becoming easier to add nodes to the cluster I have two options:
- ### Add Node with vanilla state:
	Add node with vanilla state and execute all the commands from the very start in the same order until we finish
	![](/public/072ddf2675726a7e000ae99e00c6595322a24c588f68c2c76c97c6e88387e05a.png)
 
- ### Take a snapshot from an existing node (take fresh state)
	Take a snapshot from the existing node and remember the last command executed on this node then execute the following command after the last command remembered 
	![](/public/79945ac2c8735b17940b4a5d5b324f3d955c7a645cc0eae1424c059dc15dc08f.png)

## Use timestamp to achieve the total order
Assume we attach the timestamp to each command from the client, the problem is that the client has a different time.
So attach the timestamp from the server, if all the servers have the same physical time even to the nanosecond what happens if two commands have the same timestamp I will need a deterministic algorithm to order them, and there are a lot of problems when dealing with the physical time.
## Use Sequence number to achieve the total order
But the idea remains the same I need some way to order the command by (sequence number for example) but there is universal agreement among all the nodes in the cluster on that sequence, but who is responsible for putting that sequence number ***Leader*** or If node get a sequence number all node must agree on this.

---
# Log Data structure
We need a data structure to store that kind of sequence command, basically holding the command with its order it's like a queue but not really a queue this data structure is called ***Log***

Logs: “A log is perhaps the simplest possible storage abstraction. It is an append-only, totally-ordered sequence of records ordered by time”. [Jay Kreps](https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying).
![](/public/d9138ff760438424e55e85c0b040b27a80f8a0260a9efbb9e3b05dbe33eb2f8c.png)

---
# Paxos Algorithm
***What is important here is to understand the read and write quorum (majority for read and majority of write)***
The simplest idea about paxos is every write we need a leader for that write, It can be lead election for the sequence number.
## Write Command(set, remove) Write quorum 
Assume we have 3 clients (c1, c2, c3) and 3 nodes (n1, n2, n3), in Paxos any client can send a command to any node, so we will assume that c1 sends command set(k1, v1) to n1 what will happen after that is n1 will send ***prepare*** (I'm n1 can I have sequence number 1 to execute command set(k1, v1)) to all other nodes, n1 will wait until it gets what we called ***promise*** (the node which sends promise will not accept any sequence less than or equal to 1) n1 needs the majority of the ***promises***, so in our case, I need at least one node to send ***promise*** (*It's recommended to have odd numbers of node*) 
![](/public/5fe81017325735b566f3c6f6944ed819ad54cb9a032097913ba18de39230a5b7.png)

## Read command(get, contains) Read quorum
When I want to read the client must send the read command to all nodes and get the majority of the read because I command write with the acceptance of the majority so when I read I must get the majority of the reads.
![](/public/0bb922c1197241d85964943dedc2da5d87c754679d573ed980bf22cfeeeb48f2.png)

## Conflict Resolution
When there is a conflict, for example, n1 tries set(k1, v1) and also n2 who will win the sequence number, it depends on who will get the promise first n2, for example, will receive conflict from n1 and n3 and that node knows to own its largest sequence number promised to the other node n5 promised 5 and n3 promised 3 for example so n2 will try again (***called consensus round***) with sequence number 6 and so on until he gets a sequence number. There is a lot of back and forth and a lot of latency (this time the client is waiting) so there is a lot of optimization happening.
It's like [Two Phase Commit in the database](https://martinfowler.com/articles/patterns-of-distributed-systems/two-phase-commit.html)
![](/public/337d3aa03fe2dc43122aaa69f0bce4078e9a19d1c079eaf0f6dc2333103fb3b6.png)
