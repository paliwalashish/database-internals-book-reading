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
