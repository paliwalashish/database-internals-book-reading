## Open Questions

- Examples of HTAP databases
- Wide column stores is not very clear
- File formats like ORC/Parquet, store additional metadata about data in structures, which helps with query planning/data pruning. Do they fall under indexes in broader term?
- Real life examples of Index organized file, heap organized and hash organized files
  - From ML Thread information, some details [here](https://news.ycombinator.com/item?id=34165249)
  ```
  From ycombinator

  InnoDB is similar to SQLite all tables are clustered indexes, if you don't have suitable primary key it creates 
  a hidden one similar to rowid in SQLite. MSSQL and Oracle give you a choice for table storage to either have 
  unordered heaps or ordered b-trees, depending on whether a clustered index is defined.
  PG only has unordered heaps, indexes are always secondary data structures. 
  PG could really use true clustered indexes as an option.
  ```

- clustered index or clustering need more reading
