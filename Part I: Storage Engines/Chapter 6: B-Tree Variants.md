# Chapter 6: B-Tree Variants

Common Themes for B-Trees

- Tree Structure
- Balancing
    - Splits
    - Merges
- Lookup
- Delete
- On Disk Representation

## Chapter Mindmap

<img width="2159" alt="B-Tree Variants" src="https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/d05d6c3b-185f-4dea-962b-e1810b1e9ca5">


## B-Tree Variants

![btree_vars](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/dbf3c936-5f52-4577-bc3f-bfbd2298e5b7)

- *Copy-on-write B-Trees* - Nodes are immutable. For updates, copies are made, updated and written to new locations
- *Lazy B-Tree* - Reduces I/O requests for same node by buffering updates to nodes
- *FD-Trees* - buffers updates in a small B-Tree, Full Tree is written into a immutable run
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

Lightning Memory-Mapped Database (LMDB) implements Copy-on-write approach

- LMDB single level data store, directly from memory-map
- Doesn't have DB level caching

Update process

- Identify the target leaf Node to be updated
- Copy all the Nodes in the path
- Update the target leaf and propagate changes upwards

LMDB keeps 2 version of root node - latest version and one with updates in progress.
All reads go through latest version
Once update is completed, latest version is replaced with new version

## Lazy B-Tree

*Motivation* - Reduce the cost of updating B-Tree

Adopts lightweight, concurrency and update friendly approach by  buffering and delaying updates

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
- Propagate changes to lower level
- If lower level fill up, continue till we reach leaf level
- Batch operations are performed at Leaf level, including Tree Structure changes

Schematic representation, when root buffer is not full

![LazyTreeInitial](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/fbef7624-59a7-4bed-b86d-dcda9251a89d)

## FD Tree

Small mutable BTree and multiple immutable sorted runs.

- Buffers updates in B-Tree
- Once Tree is full, it is written to a sorted immutable file
- The immutable files/runs are merged, if they reach a certain Threshold
- Pointers between levels are maintained by Fractional Cascading

![FDTree1](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/7aec2404-c4b6-477d-a551-57130ea29da9)


### Fractional Cascading

What is the problem we are trying to solve?

Since FD Tree write sorted immutable runs/files, how do we located entries in the Sorted Arrays?

FD Trees use Fractional Cascading to quickly find the element. The idea is to build bridges between neighbor levels. Bridges are build by pulling elements from lower level to higher levels, if they don't exist and pointing to the location in pulled array.

Let's look at the example array below

![FC1](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/c59d5fac-c005-413f-a74a-dbb8f664036e)


We choose to make bridge with every alternate array (highlighted)

![FC2](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/d5f04ddd-872a-451e-b51a-dc5d5d139a62)

After building fences, this is how final array looks like

![FC3](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/d2bc4960-ed20-49c1-85ee-059459fbe8d9)


### What about Run Size?

FD Tree create logarithmic size sorted runs i.e array size increases by a factor of k

- Mutable Tree accepts inputs
- Once Tree becomes full, contents are merged to Level 1 run
- Once Level 1 run reaches a threshold, it's contents are merged with Level 2 run and so-on
- FD Trees uses adaptive version of Fractional Cascading, where head element is propagated as bridge element (in our example above we used every alternative element)
- To handle multiple entries of same key, a Tombstone is inserted, which leads to entry being discarded at lowest level

## Bw-Trees or Buzzword-Tree

Breaks a Tree node into smaller parts, which are written in append-only manner

Motivation is to Address
- Write Amplification - updates to Node require updates to disk page
- Space Amplification - reserve extra space to make updates possible
- Solving concurrency problems

Bw-Tree batches updates to different Nodes by append-only storage, link nodes as chain and use in-memory structure that allows pointers between nodes with atomic swap operation. Atomic swap avoid need of Lock

### Update Chains

- Create a Base Node
- All modifications create delta nodes, linked together as linked list to the base node
- Since nodes are of different sizes, they can be stored together as they won't be page aligned

Although this is space efficient, read path has to traverse all the nodes

A simple representation of Node would be something like this

![BwTreeNode](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/8a9349df-6e0a-4df3-b831-402686b8221b)

Each Node has a logical identifier. There is an in-memory mapping table that maintains mapping between identifier and disk location.

Simple example of BW-Tree (all nodes not shown)

![bwtree](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/adcd0a95-0367-4676-b5ab-336aef87572c)

Update Process

1. Find target leaf node, start from root and traverse down with help of mapping table
2. Create a new delta node and attach to the last node (base node or delta node)
3. Update Mapping Table with delta node created in Step #2

A Compare and Swap operation is performed in in-memory structure, which avoid use of Latches.

### Structural Modifications

Like a B-Tree, BW-Tree may also need Nodes to be split or merged.

#### Split Operation

- Merge delta nodes with Base node
- A special split delta node is added to the node being split, for readers
- Split delta node invalidates records in the splitting node and has link to new logical sibling node
- Parent is updated with newly created node

## Cache-Oblivious B-Trees

- Treats on-disk structures very similar to in-memory structures
- Gives asymptotically optimal performance
- Doesn't need to know details about block size, disk page or cache lines

Disk is partitioned into blocks and data is transferred between disk and cache as blocks

### Van Emde Boas Layout

- A Cache-oblivious B-Tree consists of a static B-Tree and a packed array Structure.
- Tree is split at the middle of the edge and this process is recursive
- N square subtrees are formed
- Recursive tree is stored in contiguous memory
- Contains gaps for future inserted elements
- If gaps are not present, elements have to be moved
- If array is too dense or sparse, tree is rebuilt

#### Merging
- A special remove delete node is appended to right sibling, indicating merge
- A merge delta node is created on left sibling to point to the content of right sibling
- Parent is updated by removing pointer to right sibling

