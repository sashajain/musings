# ConnectorX

**Accelerating Data Loading From Databases to Dataframes. VLDB 2022.**

Xiaoying Wang, Weiyuan Wu, Jinze Wu, Yizhou Chen, Nick Zrymiak, Changbo Qu, Lampros Flokas, George Chow, Jiannan Wang, Tianzheng Wang, Eugene Wu, Qingqing Zhou. 

**TLDR: A leading tool to load data from a database into a data frame.**

Supported sources (OLTP and OLAP):  
- Postgres
- Mysql
- Mariadb (through mysql protocol)
- Sqlite
- Redshift (through postgres protocol)
- Clickhouse (through mysql protocol)
- SQL Server
- Azure SQL Database (through mssql protocol)
- Oracle
- BigQuery
- Trino

Supported dataframes: Polars, Pandas, Dask, Modin

Pandas is the most widely used dataframe library in Python. Loading data from a database into a Pandas.DataFrame using the Pandas.read_sql call can be highly inefficient; the study experienced the following overheads:
- 13x time overhead
- 4x memory overhead

Transferring data from a remote database into a pandas dataframe takes the following steps:
- the server side runtime:
  - query execution
  - serialization
- network transfer
- the client side (>85% of time):
  - deserialization (to basic python objects)
  - conversion into a dataframe.
  
On the client side, 
- read_sql executes each step sequentially, wasting time 
  - client waits until all returned bytes are ready in a local buffer;
  - when the client side derives part of Python objects, it does not convert them to a dataframe right away but waits until all Python objects are available
- all intermediate results will be temporarily kept in memory, which also wastes too much memory
- the intermediate result is stored in Python objects with headers that add some overhead on the size of the data (eg. integer data takes 8 bytes, whilst header for this value has 20 bytes)
- single thread execution cannot make full use of network and computational resources.

Client-side optimizations are sufficient to dramatically reduce data loading costs.

Chunking (loading data chunk by chunk and appending them) helps, but this requires extra code and tuning.

Learning from chunking, ConnectorX adopts a streaming work!ow, where the client loads and processes a small batch of data at a time. In order to avoid the extra data copy and concatenation at the end, ConnectorX adds a Preparation Phase to its work!ow. The goal of this phase is to pre-allocate the result dataframe so that the parsed values can be directly written to the corresponding final slots during execution.

Preparation phase:
1. query metadata of the query result, including the number of rows and the data type
for each column. 
1. construct the final Pandas.DataFrame by allocating the NumPy arrays accordingly. 
2. partitioning the query for parallel execution to leverage multiple cores on the client machine 

Execution phase:
Each partitioned query to a dedicated worker thread, which streams the partial query result from the DBMS into dataframe independently in parallel. Specifically, in each iteration of a worker thread: 
4. fetches a small batch of the query result from the DBMS, 
5. converts each cell into the proper data format (Rust was chosen for this in part due to the space inefficiency of Python objects), and 
6. writes the value directly to the dataframe. 
This process repeats until the worker exhausts the query result.

Client Side partitioning downsides:
1. User Burden. To enable query partitioning, user has to take extra effort to specify a
partitioning scheme over the query result
2. Load Imbalance. If the result is not evenly partitioned, stragglers may arise, hurting overall performance. 
3. Data Inconsistency. Since subqueries are sent to the server from independent sessions, their results may be derived from different snapshots.
4. Wasted Resource. Di#erent su
5. bqueries may share the same costly subplan (e.g., full-table scan). Since the DBMS processes each query independently, the sub-plan may be repeatedly executed for many times


Server-Side Result Partitioning is proposed, and it would bring the following advantages:
1. Does not need the user to input any extra information. 
2. With the help of internal  statistics and a cost estimator, the DBMS has a better chance to partition the result more evenly. 
3. It can also easily guarantee data consistency and avoid wasted resource since the DBMS has all the necessary information to partition and conduct executions on the same database snapshot


Experiments show that ConnectorX significantly outperforms Pandas, Dask, Modin and Turbodbc in terms of both speed and memory usage under di#erent scenarios