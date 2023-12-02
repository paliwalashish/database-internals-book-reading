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
