# Chapter 1: Storage Engine

## Mind Map


<img width="1875" alt="storage_engine" src="https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/e6f02f4a-9753-4359-ac92-7646be65ec0f">

Based on the usage Database Systems can be broadly classified as

OLTP
- Large number of User queries
- Queries are usually short and pre-defined

OLAP
- Used for Analytics and Data Warehouse
- Complex, long running and ad-hoc queries

HTAP
- Hybrid of OLTP and OLAP

### General DBMS architecture

- Tranport Layer
    - Handles client communication
    - Handles cluster communication (if applicable)
- Query Processor
    - Parses the query and validates it
- Query Optimizer
    - eliminates redundant parts of Query
    - determines efficient ways to execute the query
    - finalize the execution plan
- Storage Engine
    - Responsible to managing actual data
    - Consists of several sub-modules
        - Transaction Manager
        - Lock Manager
        - Access Methods
        - Buffer Manager
        - Recovery Manager

### Memory vs Disk Based

- Memory based
    - Data kept in memory
    - use disk for recovery
- Disk Based
    - Store data on disk
    - Use memory for caching
    - Manage data files , instead of relying on OS
### Column vs Row Oriented

- Row Oriented
    - Data stored as row's
    - Good to use when we need to read all fields together
    - Record/Row is identified by a unique key
    - Good spatial locality
    - Not good, when we have to read few columns for rows together
    - Ex MySQL, PostgreSQL
- Column Oriented
    - Stored data vertically i.e columns together, value of same column are stored together
    - Good for analytical workloads
    - Additional metadata need to be stored to convert into rows


### Data Files

Stores the data and should allow quick operations. DB Systems manage files on their own due to reasons below
- Storage Efficiency - min storage overhead per record
- Access Efficiency - Ability to locate records in smallest steps possible
- Update Efficiency - Ability to update record(s) with minimal changes on disk

Data files can be implemented as
- Index organized files
    - Store data in index itself
    - records are stored in key order, hence helps range scan
    - reduces number of disk seeks
- Heap organized files
    - do not follow any particular order
    - mostly use write order
    - require additional index structure to locate data
- Hash organized files
    - records are stored in buckets
    - hash value of key determine bucket
    -

### Indexes

Data Structures used to locate records like BTree, without the need to scan all the records. Helps to efficiently locate the records

Db Systems usually have separate Data and index files

#### Primary index
- index on primary key(s)

#### Secondary index
- index on other fields
- can directly point to record or primary index of the record

Storage Structure Variable

#### Buffering
- Data is buffered in memory before writing to persistent storage
- Efficient to wrote full blocks, to amortize IO cost

#### Mutability
- if updates are done on files and written (in-place updates) or use files as append only
- Copy on write can be used to implement immutability

#### Ordering
- Data is stored in key order or not
- determines if we can do range scan's or not
