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
