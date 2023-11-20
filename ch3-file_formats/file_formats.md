# File Formats (Work in Progress)


## Motivation

The strongest motivation is Efficiency, which needs control of how data is organized, stored.
Memory is byte addressable, while Disk are block addressable hence layout matters a lot.

## Data Encoding

Data at rest in disk need to be encoded to be read and interpreted again. Encoding plays an important role.

### Numeric Data Encoding

Broadly we have 2 types of byte orders in which data is stored

- Big Endian - MSB is stored at lowest address space
- Little Endian - LSB is stored at lowest address space

TODO - Add Image here

TODO - Add code references here

### Integer Types

Following are the integer types used

| Type        | Stoarge used  |
| ----------- | -----------   |
| byte        | 1 byte        |
| short       | 2 byte        |
| int         | 4 byte        |
| long        | 8 byte        |

### Floating Point Numbers

Floating point numbers *float* and *double* are represented by IEEE 754 standard

![FloatingPointRep](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/782c89b5-015f-4c0d-924d-453bafbfaa39)

*The Image is from Database Internals Book*


### Strings

Strings are stored as length + data or null terminated string.

String => Length (2 byte) + Data

For simplicity, we shall only consider Strings of the above format.

Example

```
String => Database Internals

----------------------------
|0x12  | Database Internals|
----------------------------
```
_Note_: Here we have not considered the String encoding for simplicity. 


### Bit Packed Data

*Boolean* - can be used as 1 bit, 0 - false, 1 - true

*Enums* - Integers can be used to represent enums

*Flags* - represents nonmutually exclusive names. We can use *&* operation to find the value

### Other Data Types

- null
- Date time
- Binary data

## Storage Structure

Database can have single file or multi file storage. 

Typically, we have Database storage as 

File(s) - One or more file that stores data or index
Page(s) - Files are divided into fixed size pages which stores data/index

![File_page](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/a25a4147-f9a4-41e4-84e3-6c5444a4d613)



## Storage Manager

- Maintains File/Pages
- Schedules read/write of pages
- Does not deal with redundancy

### Page
- Fixed size
- can hold data/metadata
- usually self conatined
- has unique id


## Page Management

DBMS maintains pages in different ways
- Heap File Organization
- Tree File Organization
- Sequential/Sorted File Organization (ISAM)
- Hashing File Organization

Storage Manager has to support following operations for Pages
- Create
- Update
- Write
- Delete

### Page Layout

Page layout determines how data is stored inside pages

Approach #1: Tuple Oriented Storage
Approach #2: Log Structures Storage
Approach #3: Index-organized Storage


### Tuple Orineted Storage

#### Fixed Size Tuples
- Store Tuple one after other
- Doesn't allow variable size data
- Waste a lot of space

#### Slotted Page

To conserve space and support variable size tuple, slotted pages are used.
Idea is to maintain an Slot Array to maintain pointer to Tuples. 
Each slot entry is empty or points a valid Tuple within the page
This approach does need defragmentation to reclaim space

![slotted_page](https://github.com/ashishpaliwal007/database-internals-book-reading/assets/148831617/3ed528a5-8254-4e63-af48-af715d893d14)

#### Tuple or Cell Layout

Using the metadata, we can add meaning to the Tuples. Like for B-Tree we can use the Tuple to store node information

The Last section of book Cell Layout is specific to B-Tree and it makes more sense to cover it along the B-Tree implementation in the next chapter
