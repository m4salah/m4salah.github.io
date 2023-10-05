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

