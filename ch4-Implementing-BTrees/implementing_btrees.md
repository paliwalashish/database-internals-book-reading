# Implementing B-Trees

## Page Headers

Page Header holds information used for following but not limited to
- Magic Number
- Navigation
- Maintainance
- Optimization
- Flag for Page Content and Layout
- Numbers of Cells
- Lower and Upper offsets for empty space
- additional metadata


### Magic Number

- Multibyte block, a constant value
- used to signal page, kind etc
  
## Sibling Links

- Optional implementation choice to connect to Nodes on the same level
- Avoids going back to parent node to get reference to next Node
- Disadvantage being the updation in case of splits and merges

### Node High Keys

- Highest possible key present in subtree under current node (Blink Tree)
- The Pointer points to the Key


## Overflow Pages

Pages used for allocating/storing data

- Node size and tree fabout are fixed
- Way of building variable size nodes, without copying data to contiguous regions

- Example
  - We have 4K page and with few insertions it may grow beyond 4K
  - We allocate extension 4K pages and link to original page


Estimating Node payload bytes

Node size / fanout

B-Tree implementation usually spill the data to overflow pages.

Payload larger than `max payload size`, insert the payload into overflow page, if exists, else create new one.

## Binary Search

Binary Search is used with indirection pointers.

Pseudocode

- Get the middle of middle cell
- Locate data and compare key with search key
- Based on comparison, we decide to move left or right
- Process is recursive till we find the element or insetion point

![binary_search](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/6e9f9d4e-029d-4075-aa8b-c6a1367cb07e)

*Image from Database Internals Book*

### Breadcrumbs

- References to nodes traversed from root
- Used to backtrack in case of split's or merge

- ## Rebalancing

- Postponing merges/splits to amortize operation costs
- Balancing can be performed during node insertion or delete
- Different approaches to rebalance
  - Split node into 3 (each node is 2/3 full)
  - They need additional bookkeeping
 
## Right-Only appends
- Used for monotonically increasing value as Primary key
- Optimization as insertions are happening towards the end of index
- PostgreSQL (fastpath)
- SQLite (quickbalance)

## Bulk Loading
- We can tale idea from Right-Only Append
- Works when Data is pre-sorted
- One approach is write data at leaf level and propogate it's 1st key as parent
- Avoid merges and splits, as tree is build from leaf to root

## Compression

- Granularity of compression affect operations, as they have to be uncompressed for operations and then compressed again
- Ideal is to compress page wise
- Compressed page may occupy less disk space
- Pages can spans across disk blocks

  ## Vacuum and Maintainance

  - Slotted pages require maintainance to avoid empty spaces and slots
  - Process starts from root and see the referenced data, marks the unreachable data as garbage which can collected
  - This often lead to re-writing of the pages
  - The process is known as Compaction, Vacuum or maintenance
