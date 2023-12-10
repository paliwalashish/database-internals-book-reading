# B-Tree Variants

Common Themes for B-Trees

- Tree Structure
- Balancing
  - Splits
  - Merges
- Lookup
- Delete
- On Disk Representation

## B-Tree Variants

![btree_vars](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/dbf3c936-5f52-4577-bc3f-bfbd2298e5b7)

- *Copy-on-write B-Trees* - Nodes are immutable. For updates, copies are made, updated and written to new locations
- *Lazy B-Tree* - Reduces I/O requests for same node by buffering updates to nodes
- *FD-Trees* - buffers updates in a small B-Tree, Full Tree is written into a immitable run
- *Bw-Trees* - separate B-Tree nodes into smaller parts written in an append-only manner, reduces cost of small writes by batching
- *Cache-oblivious B-tree* - treat on-disk data structures very similar to in-memory ones


## Copy-on-Write B-Tree

- Page is copied, when the content is to be modified. Copied page is modified and parallel tree hierarchy is created
- Once Tree is created, top most page is atomically updated
- Reads can happen in parallel, while the content is modified
- Simple approach, doesn't require a Locks
- Requires more space

![Copyonwrite](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/dba4466e-0ff9-443a-8607-8f0e45a38b5c)


#### LMDB

Lightning Memory-Mapped Databae (LMDB) implements Copy-on-write approach

- LMDB single level data store, directly from memory-map
- Doesn't have DB level caching

 Update process

 - Identify the target leaf Node to be updated
 - Copy all the Nodes in the path
 - Update the target leaf and propgate changes upwards

LMDB keeps 2 version of root node - latest version and one with updates in progress.
All reads go through latest version
Once update is completed, latest version is replaced with new version

## Lazy B-Tree

*Motivation* - Reduce the cost of updating B-Tree

Adopts lightweight, concurreny and update friendly approach by  buffering and delaying updates

### Wired Tiger

- MongoDB Storage engine
- Uses different format for in-memory and on-disk pages
- Updates are saved to in-memory buffer
- Page is reconciled with buffer before writing

![Wiretiger](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/f0d59426-71cb-4d5b-9757-0e78e0c94c74)

Update Buffers are accessed during reads
Update buffer us merged with page content upon flush
Update Buffer are implemented as SkipLists

### Lazy-Adaptive Tree

Optimized version to keep buffers at subtree level, instead of Node level

Update Process

- Add update to Root node buffer
- Buffer becomes full or reach a threshold storage
- Propogate changes to lower level
- If lower level fill up, continue till we reach leaf level
- Batch operations are performed at Leaf level, including Tree Structure changes

Schematic representation, when root buffer is not full

![LazyTreeInitial](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/fbef7624-59a7-4bed-b86d-dcda9251a89d)


