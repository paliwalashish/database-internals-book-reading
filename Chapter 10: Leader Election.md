# Chapter 10: Leader Election

<img width="1229" alt="Leader Election" src="https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/205f2b80-f6cb-46cd-9683-258e6a072661">


## Motivation

Contacting each participant in a Distributed System for a decision is quite expensive and a lot of round trip message.
To avoid this, some Algorithms rely on existence of a leader (or Coordinator) process, responsible for coordinating steps in distributed algorithm execution.

Generally,

- Process are uniform
- Any Process can assume role of leader
- Leadership is assumed for long time
- Leadership is not permanent

_Liveness_ of an election Algo gurantees that at one time there is only one leader and election eventually completes.

Election is triggered 
- During initialization
- After Leader process crash

## Bully Algorithm

- uses process rank to identify the new leader
- Each process is assigned a rank
- The process with the highest rank becomes the leader

### Pro's
- Simple

### Con's
- Starves process with lower rank
- Violates safety guarantees in the presence of network partition
- Unstable higher rank process can cause a lot of re-elections

### Algorithm

- Process send election message to processes with higher ranks
- Process waits, for higher ranks to respond
- If higher rank response, it does nothing. Higher rank process does next step
- Process assumes the role of leader and notify all lower rank process

## Next-In-Line Failover

### Algorithm
- Each elected leader provides list of failover nodes.
- Upon failure, election is started
- Process sends message to the highest ranked alternative provided by leader
- If one of the proposed alternatives is up, it becomes leaver
- If the detecting process is the highest ranking alternative, it can become Leader right away

### Pro's
- requires fewer steps

## Candidate/Ordinary Optimization

Splits the nodes in *candidate* and *ordinary*

*Candidate* can eventually become leader

### Algorithm

- *Ordinary* starts election, contacts all *candidate* nodes
- Collect all the response and pick the highest ranking *candidate* as leader
- Notify rest of nodes for leader election
- Uses a tiebreaker variable to prevent simultaneous elections

## Invitation Algorithm

- Invite processes to join to group, rather than outrank them
- Allows multiple leaders, as each group has its own leader

### Algorithm

- Each process starts as leader of a group with itself as member
- Contact peers who are not members to join to the group
- If peer process itself is leader, two groups are merged
- Else contacted peer responds with group leader allowing merging
- Leader of larger group can become leader of new group

## Ring Algorithm

- All nodes in the System form a ring and are aware of ring topology
- Node detects leader failure, and start election process
- Election message is forwarded across the ring
- Process skips dead node and move to next one, until one responds
- Nodes contact their siblings, collecting live node set, adding themselves before passing around the ring
- The process continues around the ring
- When message reaches to the originating node, the highest ranked node becomes the leader

### Con
- Doesn't guarantee safety
