# Chapter 9: Failure Detection

<img width="1700" alt="Failure Detection" src="https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/ffc694fd-d1aa-4a2e-a548-a25718abcb27">

Motivation

Failures must be detected in a timely manner, so that System can function as specified

Failure can broadly as

- Stopped Process
  - Dead
  - Failed
  - Crashed
- Suspected Process
  - Unresponsive
  - Faulty
  - Slow


Failure can happen at
- Link Level
- Process Level
It is very hard to distinguish slowness from failure.


*Failure Detector* is a local system responsible for identifying failed process to exclude it from Algorithm

Algorithm's ability to solve and provide correct output depends upon
- Liveness - A specific intended event must occur
- Safety - an unintended event will not occur
- Completeness - Algorithm should be able to make progress and eventually reach it's final result
- Efficiency - how fast the detection happens
- Accuracy - should detect failure correctly

Common implementations
- Heartbeats - Process actively sends notifications to it's peers
- Pings - Send a message to remote peer and expect a response within specified time

## Timeout-Free Failure Detector

- doesn't rely on in timeout for failure detection
- Ex. Heartbeat allows process failure based on data in heatbeat counter vectors

### Algorithm

#### Assumptions
- Two correct processes are connected with fdair path (contains only fair links)
- Process is aware of the existence of all other process in network
- Each process maintains a list of neighbors and counters associated with them

#### Procedure
- Process starts by sending a heartbeat to it's neighbors
- Heartbeat contains a path traveled so far
- Receiving process increments counters for all participants present in the path
- Receiving process send appends itself to the path and send heartbeat to members not present
- Message propagation stops as soon as everyone has received the heartbeat.

### Outsourced Heartbeats

- Does not require knowledge of all process in the network
- Only a subset of connected peers need to be known
- Distributes responsibility of failure detection across multiple members

### Phi-Accrual Failure Detector

- Does not treat node failure as binary problem (up/down)
- Has continuous scale, capturing probability of process crash
- Maintains a sliding window, collecting arrival of Heartbeats from peer processes
- Calculates approx arrival of next heartbeat
- Compares with actual arrival and compute suspicion level &Phi; certainty of failure given current network condition
- If &Phi; reaches a threshold, node is marked as down

#### Subsystems

- Monitoring - collects liveness information through pings/heartbeat
- Interpretation - Decision-making to mark process as suspected
- Action - Callback executed when process is marked as suspected

### Gossip
- Each member maintains a list of members, their heartbeat counters and timestamp (when the counter as incremented)
- Periodically each member increments its heartbeat counter and distributes the list to a random neighbor
- Upon receipt, node merges the list with its own
- If anode has not updated counter long enough, its considered failed
- Can cause false-positives

### Reverse Failure Detection Problem

- Costly to propagate failure to all nodes
- Arrange all active process in groups
- Failure is propagated as group failure




