# Transaction Processing and Recovery

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

