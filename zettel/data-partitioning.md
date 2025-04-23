---
created: 2025-04-23
summary:
---

# data-partitioning

## content

### Performance considerations

Partitioning Datasets affect performance by increasing files and directory structure. 

#### guidelines

Most systems, including Arrow, should work across a range of file sizes and partitioning layouts, but there are extremes you should avoid. These guidelines can help avoid some known worst cases:

- Avoid files smaller than 20MB and larger than 2GB.
- Avoid partitioning layouts with more than 10,000 distinct partitions.

#### benefits and cost of partitioning

- benefit
  - read and write in parallelism
  - nested folder structures allow to prune which files are loaded in a scan
- cost
  - each file adds overhead in processing for filesystem interaction
  - increases the overall size (shared metadata, e.g. [[parquet]] files share schema)
  - nested folder structures creates overhead (recursive list dir)


## refs

### up

- [[arrow]]
- [[r-arrow]]

### down

- [[parquet]]

### sources

- [[docs-r-arrow-multifile-data]]
