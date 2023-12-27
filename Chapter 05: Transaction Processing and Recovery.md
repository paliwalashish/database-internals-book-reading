# Chapter 5: Transaction Processing and Recovery

# What is a Transaction?

A Transaction is
- Indivisble logical unit of work, either all steps are executed or none
- May have multiple steps including read and write of records
- Must have ACID properties

### ACID Properties

#### Atomicity
Transaction Steps are indivisble, either all or none. Can be committed or rolled back.
Transaction can be retried, in case it was aborted

#### Consistency
Trasnaction should only bring Database from one valid state to another valid state, including maintaining all invarients

#### Isloation
Multiple Transaction can run in parallel. Isolation defines when DB state changes become visible to concurrent transactions

#### Durability
Once committed, the state is persisted and can survive system failures and crashes

### Transaction Manager

Trasnaction Manager cooridnates and schedules Transactions.

Several Systems participate in achiving Transaction, some of them are listed below

#### Lock Manager

- Guards Access to a resource
- Prevents concurrent Access to ensure Data integrity
- Grants Lock in shared or exclusive mode

#### Page Cache

- Intermediate between disk and Storage Enginer
- Stage area for changes to be written to persistance storage
- All changes are applied first to Page cache

#### Log Manager

- History of Log entries applied to page cache but not yet written to disk
- Act as undo-redo log

## Buffer Management

Some Terminology

*Buffer pool* - Pool of Pages in memory
*paged in* - Loaded from disk to memory
*dirty* - Page cntext modified
*flushed* - Page written back to persistent storage

#### Page Cache

- Keeps cached page content
- buffers modification
- Manages Page in requests
- Evicts the pages when needed


## Caching Semantics

Only Buffer Manager makes changes to persistance store, hence it has control of page cache
It gives DBMS more control over memory management and disk accesses including pre-fetching

### Cache Eviction

Page Cache is in memory, so crash can lead to Data Loss.
To avoid this *Checkpointing* is used. Operations are written to WAL and works in coordination till it's safe to apply changes

Trade-off

- Reduce flushes for less disk access
- quick eviction but frequent disk access
- evition and flush balance
- Maintain memory boundaries for Cache
- Avoiding Data loss

#### Locking Pages

Some pages are always needed, we can pin them in cache to avoid page-in/out like higher level nodes in tree

### Page Replacement

We need efficient Page Replacement policies to manage Pages.

#### FIFO

- Maintain a queue of page id in the insertion order
- does not account for page access

#### LRU
- Least Recently Used
- Queue of eviction candidate in insertion order, allows to put page back at tail
- expensive updating and relinking nodes in concurrent environment

### Clock

CLOCK-sweep holds references to pages and assocaited access bits in circular buffer

- each access increments the counter

Algo to evict

- Iterate the circular buffer
- Inspect access bit, if it is 1 and unreferenced, set to 0
- If bit is 0, schedule for eviction
- If page is referened, access bit remains unchanges

Algo is simple and does not require additional locking

### LFU

Least Frequently used. We can start tracking page reference events and evict the ones least used.

## Recovery

WAL or Write Ahead Log is a append-only disk resident structure used for crash and Transaction recovery.
WAL writes changes to disk, however the Page buffer keeps pages in memory till they are flushed.

WAL does
- allow page cache to buffer changes in memory ensuring semantics
- Persist all opeartions on disk
- Allows lost in-memory changes to be reconstructed from disk in case of failure


### Log Semantics

WAL is
- append only
- immutable
- sequence of log records
- has Log Sequence Number (LSN)
- Holds record of transaction completion

*Sync checkpoint* - process that forces all dirty pages to be flushed on disk

Steal Policy - allows a modified page by transaction to be flushed before commit
No-Steal Policy - does not allow uncomitted transaction on disk
Force Policy - all modified pages from transaction to be flushed to disk before commit
No-Force Policy - allow transaction to commit even without page flush
