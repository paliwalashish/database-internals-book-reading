# Chapter 2: B-Tree Basics

Mind Map for the chapter

<img width="1854" alt="B-Tree" src="https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/326154d9-7c3b-419a-a6d3-cd6ad75de6a6">


## Issue with BST on disk
- locality - elements may span multiple disk pages
- can overcome above issue by using paged Binary Trees
- Cost of search is log n, and corresponding disk transfers, due to fanout of 2


## Disk based Structures

### Disks
- HDD
	- expensive random seek
	- seek is costly
	- sequential read is cheap
	- Min unit to read is Sector (512 bytes to 4KB in size)
- SDD
	- No moving parts
	- Construction
		- Memory Cells -> Strings (32-64 cells) -> Arrays -> Pages -> Blocks -> Plane - Die
		- SSD has 1 or more Die's
	- Page size is 2-16 KB
	- Block has 64-512 pages
	- Page is smallest Unit for read/write
	- Smallest erase entity is block
	- Updated pages are to be written to empty
	- GC may affect write operation

### On Disk Structures

#### Challenges
- Disk access cost
- Cost to update pointers across blocks
- Offsets are precomputed, hence increase complexity for long chain


#### B-Trees
- Sorted
- log search
- Efficiently execute point and range queries
- Designed to use organize around page size
- Keys are stored in sorted order

 - root Node
	- parent node
- Leaf Node
	- have no child nodes
- Internal nodes
	- connects root with leaf nodes
- Index entries/Separator keys/Divider Cells


#### Lookup Complexity
- Number of block transfer
- Number of comparisons

#### Lookup Algo
- From root, perform binary search to find 1st separator key greater than search value
- Follow the pointer and repeat the process in sub-tree till leaf node
- Either we find the node or it's predecessor

#### Node Splits
- To ensure nodes can hold the Key value pairs
- For a full node, node is split it into 2 halves creating 2 nodes
- If the parent is full, process is repeated

#### Node Merges
- During deletion, nodes with too few values are merged
