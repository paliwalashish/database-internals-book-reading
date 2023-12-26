# Chapter 8: Introduction and Overview

## Concurrent Execution

The result of non-atomic computations become unpredictable in multithreaded environment, as soon as
we have a shared state. Threads access shared state, compute and propagate state back to shared variables.

![concurreny](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/0e538b17-dde5-4cc7-8abb-3d13b6db1e94)


In the above image, the result shall vary based on what value the Threads see.
It's a simple increment, however the outcome could be 2 or 3 based on how Thread execution is scheduled.

To avoid multiple outcomes, we need _*Consistency Models*_.

### Consistency Models

Consistency Models establish an order in which operations can be executed and results made visible to participant's in a concurrent environment.

### Shared State in Distributed System

*synchrony* - notion of time for the system like communication is asynchronous or some timing assumptions

*failure model* - describes which failures can occur and how to treat them like process crash

*Fault Tolerance* - systems capability to operate correctly in presence of failures

## Fallacies of Distributed Computing

#### Network is Reliable

- Connection is not always stable
- Latency is not zero
- Network components may break or malfunction
- Packet drops
- Even if initial connection is set up, messages may not reach destination
- Bandwidth is finite

Potential Solutions
- Retries
- Reconnects
- non-instantaneous execution

##### Processing
 
- Cannot assume instantaneous processing due to
  - message waiting in queue
  - different hardware configuration
  - may need information/help from several servers to complete task

*Backpressure* is strategy to cope with a fast producer, asking them to slow down by sending specific response code like 429 back

#### Clocks and Time

- Systems use local clock
- Local time can drift from real time due to hardware
- Clock source can e monotonic (cannot go backward)

#### State Consistency

Some Distributed Algorithms allow state divergence between replica's and rely on conflict resolution and read-time data repair

An eventually consistent distributed database may use quorum for resolving replica disagreement.

### Failure Handling

- Node failure need to be handled
- Heartbeat protocols and failure detectors need to be build

#### Network Partitions and Partial Failures

Network Partition - when 2 or more servers cannot communicate with each other
It's a bigger problem when the split groups can proceed with execution and produce conflicting results

We need to build system to live with failures

#### Cascading Failures

A failure leading to multiple failure within a system. Like a node crashing under heavy load and in term causing 
heavy load on other nodes, eventually leading to a cascading failure of system.

Backoff strategy can help prevent such failures. It's using some random time between retries for an operation

## Distributed Systems Abstractions

### Links

Networks are not reliable i.e messages can be lost, delayed or reordered

![message](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/218d9f6e-3eec-4499-9708-9e690240f735)


When a message is sent from System A to System B, following can happen

- Message is delivered
- Message is lost
- Message is delivered with a delay

Properties of Fair-Loss Link
- Fair loss
  - Message will be eventually delivered, if sender and receiver are correct and sender keeps retransmitting
- Finite duplication
  - sent message won't be delivered infinitely many times
- No Creation
  - It won't deliver message that was never sent

Fair Loss link is a very useful abstraction for building communication protocol

### Message Acknowledgments

- A way to improve communication
- Receiver sends an Ack for receiving the message
- Needs bi-directional communication channel
- Need to use sequence numbers to identify messages

### Message Retransmits

- Retransmit is the way for sender to resend the message 

An _idempotent_ operation is one that can be executed multiple times, yielding the same result without producing additional side effects

### Message order

- Messages can arrive out of order
- Same message can arrive multiple times due to retries

Message identifiers are used to order messages on receiver side

- n<sub>consecutive - highest seq num up to which all messages are seen and ordered
- n<sub>processed - highest seq num up to which messages have been ordered and processed

### Two Generals' Problem

Experiment to show that it's impossible to achieve agreement between 2 parties with async communication

Scene: 2 Armies are preparing to attack a fortified city. The attack can succeed only if they attack simultaneously
Army Generals communicate by sending messangers. The plan is ready, they need to agree on execution.

![2generals](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/d45bbc26-dfda-4ba2-8d88-430c754402d2)


- General A, send MSG(n) with intention to attack at specified time, if General B agrees
- General B, has to send Ack, when he received the message
- Messanger can get captured or killed
- General B needs an ACK(ACK(MSG(n)))

This depicts that no matter the case, there will always be one Acknowledgement left

### FLP Impossibility

A form of consensus in which processes start with an initial value and attempt to agree on a new value.

For a Consensus protocol to be correct, it needs to preserve three properties

- Agreement: decision has to be unanimous
- Validity: Agreed value has to be proposed by one of the participants
- Termination: Agreement is final only if there are no processes that did not reach the decision state


## Failure Models

A failure model describes exactly how processes can crash in a distributed system, and algorithms are developed using these assumptions

#### Crash Faults

- Process stops execution and stops processing messages
- Once a process has crashed, it remains in that state
- Algorithm does not rely on recovery
- failed processes don't continue to participate in the Algorithm

In _Crash-recovery_ model, process can recover after crash. The possibility of recovery requires introducing a durable state and recovery protocol into the system

#### Omission Faults

- Process skips some steps

#### Arbitrary Faults

- Arbitrary or Byzantine faults, a process continues executing the algorithm steps, but in a way that contradicts the algorithm
- due to bugs or different algorithm version
