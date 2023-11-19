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



