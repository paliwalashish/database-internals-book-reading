Scribble is very rough notes while reading
## Chapter 1 Storage Engines

Databases

- OLTP 
	- large number of user facing requests/transactions
	- predefined and short queries
- OLAP
	- used for analytics and warehousing
	- complex, long running ad-hoc queries
- Hybrid Transactional and analytical processing
	- best of both OLTP and OLAP

General DBMS Architecture

diagram here


transport system
       |
       V
Query processor
Query optimizer
        |
        V
Execution Engine

Storage Engine
- Tx Manager
- Lock Manager
- Storage Structure
- Buffer Manager
- Recovery Manager


### Column vs Row Oriented DBs

#### Row
- Data stored as row's
- Good to use when we need to read all fields together 
- Record/Row is identified by a unique key
- Good spatial locality
- Not good, when we have to read few columns for rows together

#### Column oriented
- Stored data veritically i.e columns together, value of same column are stored together
- Good for analytical workloads
- additional metadata need to be stored to convert into rows

Access pattern determines the choice
- All records read together - Row oriented
- All column(s) values read together - Column Oriented

There are other factors involved as well.

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

