# DuckDB

**An Embeddable Analytical Database**

**TLDR: Convenient, fast in-process OLAP SQL database. For analysts or embedded devices/edge-computing to query data on-disk or in-memory via SQL syntax (ie. execute SQL on Python objects)**

## What is the Need
There is a clear need for embeddable analytical data management. This needs stems from two main sources: 
- Interactive data analysis (eg. R/Python)
  - The basic data management operators available in these environments through extensions (dplyr [14], Pandas [6], etc.) closely resemble stacked relational operators, much like in SQL queries, but lack full-query optimization and transactional storage
- “edge” analytical databases (eg. sensors, embedded devices)
  - have limited bandwidth

Prior to DuckDB, they claim there was no unobtrusive in-process data management solutions (like sqlite) geared towards analytical workloads.

SQLite is notable in-process but strongly focuses on transactional (OLTP) workloads. It contains a row-major execution engine operating on a B-Tree storage format. As a consequence, SQLite’s performance on analytical (OLAP) workloads is very poor.

Stand-alone systems are popular, in part to: 
- serve requests from many clients simultaneously, 
- data integrity requirements. 
Though stand-alone systems require considerable effort to set up properly and data access is constricted by their client protocols

## Portability
DuckDB has no external dependencies, neither for compilation nor during run-time. For releases, the entire source tree of DuckDB is compiled into two files, a header and an implementation file, a so-called “amalgamation”. This greatly simplifies deployment and integration in other build processes. For building, all that is required to build DuckDB is a working C++11 compiler. It is therefore really portable.

## Components/Design
“textbook” separation of components: Parser, logical planner, optimizer, physical planner, execution engine. No component is revolutionary in its own regard. Instead, combined methods and algorithms from the state of the art that were best suited for the use cases.

## Functionality
DuckDB can ingest data from a wide variety of formats – both on-disk (eg. csv, json, parquet) and in-memory (eg. dataframes). 

DuckDB can directly query Pandas DataFrames, Polars DataFrames and Arrow tables. Note that these are read-only, i.e. editing these tables via INSERT or UPDATE statements is not possible.

DuckDB provides serious data management features. There is extensive support for complex queries in SQL with a large function library, window functions, etc. DuckDB provides transactional guarantees (ACID properties) through our custom, bulk-optimized Multi-Version Concurrency Control (MVCC). Data can be stored in persistent, single-file databases. DuckDB supports secondary indexes to speed up queries trying to find a single table entry.

DuckDB is designed to support analytical query workloads, also known as online analytical processing (OLAP). These workloads are characterized by complex, relatively long-running queries that process significant portions of the stored dataset, for example aggregations over entire tables or joins between several large tables. Changes to the data are expected to be rather large-scale as well, with several rows being appended, or large portions of tables being changed or added at the same time.

To efficiently support this workload, it is critical to reduce the amount of CPU cycles that are expended per individual value. The state of the art in data management to achieve this are either vectorized or just-in-time query execution engines. DuckDB contains a columnar-vectorized query execution engine, where queries are still interpreted, but a large batch of values (a “vector”) are processed in one operation. This greatly reduces overhead present in traditional systems such as PostgreSQL, MySQL or SQLite which process each row sequentially. Vectorized query execution leads to far better performance in OLAP queries.


Sources:
- https://duckdb.org/pdf/SIGMOD2019-demo-duckdb.pdf
- https://duckdb.org/