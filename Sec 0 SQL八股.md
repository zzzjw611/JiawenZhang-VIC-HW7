# Database & Hibernate Interview Questions and Answers

---

## 1. What is a database and what is a Database Management System (DBMS)?

**Answer:**

* **Database:** An organized collection of structured data stored electronically, allowing efficient retrieval, management, and updating.
* **DBMS:** Software that interfaces between applications/users and the database, handling data definition, manipulation, transaction management, concurrency control, security, backup, and recovery. Examples: MySQL, PostgreSQL, MongoDB, Cassandra.

---

## 2. How do you keep a database stable?

**Answer:**

1. **Provision Resources:** Allocate sufficient CPU, memory, disk I/O, and network bandwidth (e.g., SSDs for low-latency storage).
2. **Regular Backups & Recovery Testing:** Schedule full/incremental backups; periodically test restores in non-production.
3. **Monitoring & Alerting:** Track system metrics (CPU, memory, I/O), DB metrics (slow queries, replication lag, cache hit ratio), and set alerts for thresholds. Tools: Prometheus + Grafana, Datadog, AWS CloudWatch.
4. **Index Maintenance & Statistics:** Rebuild or reorganize fragmented indexes; update table statistics so the optimizer chooses optimal plans.
5. **Connection Pooling & Limits:** Use connection pools (e.g., HikariCP); set sensible `max_connections` to avoid exhaustion.
6. **Query Optimization:** Identify slow queries, add or adjust indexes, rewrite inefficient SQL (avoid `SELECT *`, use covering indexes).
7. **Transaction Design:** Keep transactions short; choose appropriate isolation levels (READ COMMITTED vs. SERIALIZABLE) to balance consistency vs. performance.
8. **Configuration Tuning:** Adjust buffer/cache sizes (e.g., `innodb_buffer_pool_size`, `shared_buffers`), checkpoint/WAL parameters.
9. **High Availability & Replication:** Use primary–replica replication to offload reads; implement automated failover (e.g., MySQL Group Replication, PostgreSQL Patroni).
10. **Apply Security Patches & Updates:** Keep the DBMS up to date; perform rolling upgrades or blue–green deployments to minimize downtime.

---

## 3. How do you monitor a database?

**Answer:**

1. **Collect Key Metrics:**

   * System: CPU, memory, disk I/O, network.
   * DBMS: query latency (p50/p95/p99), slow-query count, cache hit ratio, connection count, lock contention, replication lag, transaction rates.
2. **Use Monitoring Tools:** Prometheus + Grafana, Datadog, New Relic, AWS CloudWatch (for RDS/Aurora). DB-specific tools:

   * **MySQL:** Performance Schema, `SHOW STATUS`, `SHOW PROCESSLIST`.
   * **PostgreSQL:** `pg_stat_activity`, `pg_stat_database`, `pg_stat_replication`.
   * **MongoDB:** `mongostat`, `mongotop`, Cloud Manager.
   * **Cassandra:** `nodetool`, JMX metrics.
3. **Set Alerts:**

   * CPU > 80% sustained, buffer cache hit ratio < 90%, replication lag > 10s, disk usage > 80%.
4. **Log Monitoring:** Analyze error logs, slow-query logs via ELK, Splunk, or Graylog. Alert on critical errors.
5. **Health Checks & Synthetic Transactions:** Periodically run basic read/write queries to test connectivity and responsiveness.
6. **Capacity Planning & Trends:** Track table growth, row counts, disk usage growth over time to plan scaling.
7. **Security Monitoring:** Audit login attempts, watch for anomalous queries, review permission/grant changes.

---

## 4. What is the difference between a relational database and a non-relational database?

**Answer:**

| Aspect                                    | Relational Database (RDBMS)                              | Non-Relational Database (NoSQL)                                    |
| ----------------------------------------- | -------------------------------------------------------- | ------------------------------------------------------------------ |
| **Data Model**                            | Tables with fixed schema (rows and columns).             | Flexible schema: key–value, document, wide-column, or graph.       |
| **Schema Enforcement**                    | Strongly typed, predefined schema.                       | Schema can be dynamic or schema-less.                              |
| **Query Language**                        | SQL (Structured Query Language).                         | Varies by type:                                                    |
| • Key–Value: GET/PUT APIs                 |                                                          |                                                                    |
| • Document (MongoDB): JSON-based queries  |                                                          |                                                                    |
| • Wide-Column (Cassandra): CQL (SQL-like) |                                                          |                                                                    |
| • Graph (Neo4j): Cypher                   |                                                          |                                                                    |
| **Transactions & ACID**                   | Full ACID with strict guarantees.                        | Often eventual consistency; some support lightweight transactions. |
| **Scalability**                           | Vertical scaling (scale-up).                             | Designed for horizontal scaling (scale-out).                       |
| **Use Cases**                             | Strong consistency, complex JOINs, relational integrity. | Big data, real-time web apps, caching, analytics, IoT.             |
| **Examples**                              | MySQL, PostgreSQL, Oracle, SQL Server.                   | MongoDB, Redis, Cassandra, Neo4j, DynamoDB.                        |

---

## 5. What is consistency (in the context of distributed systems or databases)?

**Answer:**

1. **ACID Consistency:** After a transaction commits, the database transitions from one valid state to another, preserving all integrity constraints (e.g., unique keys, foreign keys).
2. **Distributed Consistency (CAP):**

   * **Strong Consistency:** Reads always return the most recent write.
   * **Eventual Consistency:** Updates propagate asynchronously; replicas may serve stale data until convergence.

---

## 6. What is availability (in the context of distributed systems or databases)?

**Answer:**
Availability means every request (read or write) receives a response—success or failure—without indefinite waiting, even if some nodes or network links fail.

* **High Availability:** System remains operational (e.g., “five 9s” uptime).
* Achieved via replication, failover, load balancing, and redundancy.
* Under network partitions (CAP), choosing availability may sacrifice strict consistency.

---

## 7. What is the CAP theorem (Consistency, Availability, and Partition Tolerance)?

**Answer:**

* **CAP Theorem:** In a distributed system, you can only guarantee two of the following three:

  1. **Consistency (C):** All nodes see the same data at the same time.
  2. **Availability (A):** Every request receives a response (without guarantee of the latest data).
  3. **Partition Tolerance (P):** The system continues to operate despite network partitions.
* **During a partition:**

  * **CP system:** Favors consistency; may refuse to serve some requests, sacrificing availability.
  * **AP system:** Favors availability; serves requests but may return stale data, sacrificing consistency.

---

## 8. When will you choose MySQL and when will you choose PostgreSQL?

**Answer:**

| Criteria           | MySQL                                                         | PostgreSQL                                                        |
| ------------------ | ------------------------------------------------------------- | ----------------------------------------------------------------- |
| **Use Cases**      | LAMP-stack web apps, simple CRUD, read-heavy workloads.       | Complex analytics, data warehousing, GIS, advanced SQL.           |
| **Features**       | InnoDB/MyISAM engines, simpler setup, broad hosting support.  | Advanced indexing (GIN, GiST), full-text search, JSONB.           |
| **Performance**    | Fast for simple lookups and basic joins.                      | Better for complex queries, joins, and analytical workloads.      |
| **SQL Compliance** | SQL-92 subset; fewer advanced features until recent versions. | Highly SQL-compliant (SQL:2003), supports window functions, CTEs. |
| **Extensibility**  | Plugins for storage engines; user-defined functions in C/C++. | Rich extension ecosystem (PostGIS, TimescaleDB, pgcrypto).        |
| **JSON Support**   | JSON data type since 5.7; limited indexing (8.0 improved).    | JSON/JSONB since 9.4; supports indexing on JSONB fields.          |
| **When to Choose** | Simple web apps, ease of setup, broad hosting; read-heavy.    | Advanced queries, custom data types, strict ACID, geospatial.     |

---

## 9. Can you list some common MySQL and PostgreSQL commands?

**Answer:**

### MySQL

1. **Connect:**
   mysql -u <user> -p
2. **Show Databases:**
   SHOW DATABASES;
3. **Create Database:**
   CREATE DATABASE my\_db;
4. **Use Database:**
   USE my\_db;
5. **Show Tables:**
   SHOW TABLES;
6. **Describe Table:**
   DESCRIBE users;
7. **Create Table:**
   CREATE TABLE users (
   id INT AUTO\_INCREMENT PRIMARY KEY,
   username VARCHAR(50),
   email VARCHAR(100),
   created\_at TIMESTAMP DEFAULT CURRENT\_TIMESTAMP
   );
8. **Insert Data:**
   INSERT INTO users (username, email) VALUES ('alice', '[alice@example.com](mailto:alice@example.com)');
9. **Select Data:**
   SELECT id, username FROM users WHERE username LIKE 'a%';
10. **Update Data:**
    UPDATE users SET email = '[alice@newdomain.com](mailto:alice@newdomain.com)' WHERE id = 1;
11. **Delete Data:**
    DELETE FROM users WHERE id = 1;
12. **Create Index:**
    CREATE INDEX idx\_username ON users(username);
13. **Show Process List:**
    SHOW PROCESSLIST;
14. **Show Global Status:**
    SHOW GLOBAL STATUS;
15. **Replication Status:**
    SHOW SLAVE STATUS\G;
16. **Grant Privileges:**
    GRANT ALL PRIVILEGES ON my\_db.\* TO 'app\_user'@'localhost';
    FLUSH PRIVILEGES;
17. **Backup (mysqldump):**
    mysqldump -u <user> -p my\_db > backup.sql
18. **Restore:**
    mysql -u <user> -p my\_db < backup.sql

---

### PostgreSQL

1. **Connect:**
   psql -U <user> -d <db>
2. **List Databases:**
   \l
3. **Create Database:**
   CREATE DATABASE my\_db;
4. **Connect to Database:**
   \c my\_db
5. **List Tables:**
   \dt
6. **Describe Table:**
   \d users
7. **Create Table:**
   CREATE TABLE users (
   id SERIAL PRIMARY KEY,
   username VARCHAR(50) NOT NULL,
   email VARCHAR(100) UNIQUE,
   created\_at TIMESTAMPTZ DEFAULT NOW()
   );
8. **Insert Data:**
   INSERT INTO users (username, email) VALUES ('bob', '[bob@example.com](mailto:bob@example.com)');
9. **Select Data:**
   SELECT id, username FROM users WHERE username LIKE 'b%';
10. **Update Data:**
    UPDATE users SET email = '[bob@newdomain.com](mailto:bob@newdomain.com)' WHERE id = 1;
11. **Delete Data:**
    DELETE FROM users WHERE id = 1;
12. **Create Index:**
    CREATE INDEX idx\_username ON users(username);
13. **Show Active Connections:**
    SELECT pid, usename, application\_name, client\_addr, state, query
    FROM pg\_stat\_activity;
14. **Show Replication Status:**
    SELECT \* FROM pg\_stat\_replication;
15. **Grant Privileges:**
    GRANT ALL PRIVILEGES ON DATABASE my\_db TO app\_user;
16. **List Roles:**
    \du
17. **Backup (pg\_dump):**
    pg\_dump -U <user> -F c -d my\_db -f backup.dump
18. **Restore:**
    pg\_restore -U <user> -d my\_db backup.dump

---

## 10. How do you handle data consistency when saving data to a database?

**Answer:**

1. **Transactions:** Group related operations in a transaction (`START TRANSACTION … COMMIT`). Roll back on failure.
2. **Constraints & Keys:** Enforce `PRIMARY KEY`, `FOREIGN KEY`, `UNIQUE`, `NOT NULL`, `CHECK`.
3. **Isolation Levels:** Choose appropriate level (READ COMMITTED, REPEATABLE READ, SERIALIZABLE) to prevent anomalies (dirty reads, non-repeatable reads, phantoms).
4. **Optimistic vs. Pessimistic Locking:**

   * Optimistic: Check version/timestamp before committing; retry on conflict.
   * Pessimistic: Lock rows during read/update (`SELECT … FOR UPDATE`).
5. **Atomic Operations:** Use single-statement updates when possible (`UPDATE accounts SET balance = balance – 100 WHERE id = X`).
6. **Idempotent Writes:** Use unique request IDs and check for duplicates before applying.
7. **Distributed Transactions:** Use two-phase commit (2PC) or compensate with eventual consistency and conflict resolution (CRDTs, vector clocks).
8. **Schema Design:** Normalize appropriately to avoid redundant data and anomalies; consider denormalization only when read performance requires it.

---

## 11. What is ACID (Atomicity, Consistency, Isolation, Durability)?

**Answer:**

1. **Atomicity:** All parts of a transaction succeed or none do; no partial updates.
2. **Consistency:** Database moves from one valid state to another, enforcing all integrity constraints.
3. **Isolation:** Concurrent transactions do not interfere; the outcome is as if transactions ran serially.
4. **Durability:** Once committed, changes persist even after crashes (via write-ahead logs, snapshots).

---

## 12. What is an atomic operation?

**Answer:**
A single indivisible operation or set of operations (transaction) that either completes fully or has no effect. In databases, a transaction is atomic: either all statements succeed and commit, or a failure rolls back all changes.

---

## 13. What is indexing in a database?

**Answer:**
An index is a data structure (e.g., B-tree, hash, inverted index) that speeds up data retrieval by storing sorted keys and pointers to the corresponding rows.

* **Types:** B-Tree (range scans, equality), Hash (exact-match only), Bitmap (low-cardinality), Full-Text (inverted), Geospatial (R-Tree, GiST).
* **Trade-offs:** Improves SELECT performance; adds storage overhead and slows INSERT/UPDATE/DELETE because indexes must be updated.
* **Composite Indexes:** Multiple columns; order matters.
* **Covering Index:** Includes all columns needed by a query so the DB can return results using only the index.

---

## 14. How do you query data from MongoDB? What are the CRUD (Create, Read, Update, Delete) syntax?

**Answer:**

### Create

* `db.collection.insertOne({ … })`
* `db.collection.insertMany([{ … }, { … }, …])`

### Read

* `db.collection.find(filter, projection).sort(sort).limit(n).skip(m)`
* `db.collection.findOne(filter)`

### Update

* `db.collection.updateOne(filter, { $set: { … }, $inc: { … }, … })`
* `db.collection.updateMany(filter, { … })`
* `db.collection.replaceOne(filter, newDocument)`

### Delete

* `db.collection.deleteOne(filter)`
* `db.collection.deleteMany(filter)`

---

## 15. What’s the difference between MongoDB and DynamoDB?

**Answer:**

| Aspect               | MongoDB                                                                           | DynamoDB                                                                  |
| -------------------- | --------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| **Data Model**       | Document store (BSON). Collections of JSON-like documents.                        | Key–value and document store with items (attribute sets).                 |
| **Hosting**          | Self-hosted or managed (Atlas).                                                   | Fully managed, serverless (AWS).                                          |
| **Query & Indexing** | Rich ad hoc queries, aggregation pipeline, secondary indexes, geospatial queries. | Query/Scan by primary key or indexed attributes; limited ad hoc querying. |
| **Consistency**      | Tunable: strong (reads from primary) or eventual (reads from secondaries).        | Tunable per request: eventual (default) or strong (higher cost).          |
| **Scaling**          | Auto-sharding, manual shard key selection.                                        | Automatic horizontal scaling; provisioned or on-demand capacity.          |
| **Transactions**     | Multi-document ACID transactions (v4.0+).                                         | Transactions (up to 10 items, 4 MB total) with ACID guarantees.           |
| **Ecosystem**        | Official drivers for most languages, Atlas Data Lake, change streams.             | Deep AWS integration: Lambda, CloudWatch, API Gateway, Streams.           |
| **Pricing**          | Pay for servers (CPU, RAM, storage).                                              | Pay per read/write unit (RCU/WCU) and storage.                            |
| **Use Cases**        | Content management, real-time analytics, hierarchical data.                       | Serverless apps, gaming, IoT, single-digit millisecond latencies.         |

---

## 16. Can you use “JOIN” in MongoDB?

**Answer:**
Not in the traditional SQL sense. MongoDB’s aggregation framework provides `$lookup` to perform left-outer joins between a “local” and “foreign” collection:

```javascript
// Example aggregation with $lookup
db.orders.aggregate([
  {
    $lookup: {
      from: "users",
      localField: "userId",
      foreignField: "_id",
      as: "userInfo"
    }
  },
  { $unwind: "$userInfo" },
  {
    $project: {
      orderDate: 1,
      "userInfo.username": 1,
      "userInfo.email": 1
    }
  }
]);
```

However, embedding related data is often preferred to avoid frequent joins.

---

## 17. Can MongoDB use Hibernate?

**Answer:**
Hibernate is an ORM for relational databases (JDBC). To use a JPA-like API with MongoDB:

* **Hibernate OGM:** Extends JPA/Hibernate APIs to NoSQL datastores (including MongoDB). Provides basic CRUD and JPQL support but lacks full feature parity.
* **Spring Data MongoDB:** Preferred for Spring projects; offers repository abstractions (`MongoRepository`) and mapping between Java objects and BSON.
* **Morphia:** ODM for Java, annotation-driven mapping.
* **Official Driver:** Directly use the MongoDB Java driver for full control.

---

## 18. What applications/tools are commonly used to work with MongoDB?

**Answer:**

1. **MongoDB Compass:** Official GUI for schema exploration, query building, performance analysis.
2. **mongosh (Mongo Shell):** Interactive JavaScript shell for administrative tasks and queries.
3. **MongoDB Atlas:** Managed cloud service with web UI, monitoring, backups, scaling.
4. **Robo 3T:** Lightweight GUI with embedded shell.
5. **Studio 3T:** Advanced commercial GUI with SQL-to-Mongo translation, visual query builder.
6. **NoSQLBooster:** Cross-platform GUI with IntelliSense, code snippets, SQL query support.
7. **Mongoose (Node.js):** ODM library providing schema-based modeling, validation.
8. **Spring Data MongoDB (Java):** Repository and template abstractions for MongoDB.
9. **PyMongo (Python):** Official Python driver.
10. **MongoDB BI Connector:** Enables SQL-based BI tools to query MongoDB data (appears as MySQL).

---

## 19. Compare MongoDB and Cassandra.

**Answer:**

| Aspect                 | MongoDB                                                                             | Cassandra                                                                                                 |
| ---------------------- | ----------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| **Data Model**         | Document store (BSON).                                                              | Wide-column store (tables with flexible columns).                                                         |
| **Schema**             | Flexible schema; documents can vary.                                                | Table schema defines columns; each row can have extra columns, but must match defined data types or UDTs. |
| **Consistency Model**  | Tunable: Strong on primary or eventual on secondaries; multi-document ACID (v4.0+). | Tunable per-query: ONE, QUORUM, ALL; eventual consistency by default; lightweight transactions via Paxos. |
| **Write/Read Pattern** | Single-primary writes; secondaries replicate asynchronously.                        | Masterless, peer-to-peer; any node can accept reads/writes, coordinated by partitioner.                   |
| **Scalability**        | Auto-sharding; manual shard key selection.                                          | Linear horizontal scaling; automatic data distribution via consistent hashing.                            |
| **Query Capabilities** | Rich ad hoc queries, aggregation pipeline, secondary indexes.                       | Query by primary key or indexed columns only; limited ad hoc querying.                                    |
| **Performance Focus**  | Balanced for reads/writes; can bottleneck on primary for heavy writes.              | High write throughput; optimized for time-series, event logging.                                          |
| **Replication & HA**   | Replica sets with automatic failover.                                               | Peer-to-peer replication with tunable replication factor; no single point of failure.                     |
| **Storage Engine**     | WiredTiger (default) with MVCC, compression.                                        | SSTables + memtables; various compaction strategies.                                                      |
| **TTL / Expiration**   | TTL indexes on date fields.                                                         | TTL per column at write time.                                                                             |
| **Use Cases**          | Content management, real-time analytics, hierarchical data.                         | Time-series data, event logging, IoT telemetry, large-scale writes.                                       |

---

## 20. What can Redis be used for?

**Answer:**
Redis is an in-memory data structure store used as:

1. **Cache Layer:** Store frequently accessed data (sessions, configs, computed results) with TTL.
2. **Session Store:** Store user sessions in web/mobile apps; persistence via RDB/AOF if needed.
3. **Message Broker / Pub-Sub:** Real-time messaging (chat apps, notifications); Streams for persistent queues.
4. **Leaderboards / Counting:** Sorted sets for ranking, HyperLogLog for approximate distinct counts.
5. **Geospatial Indexing:** GEO commands (`GEOADD`, `GEORADIUS`) for location-based queries.
6. **Rate Limiting:** Atomic `INCR` with TTL for API throttling.
7. **Full-Text Search / Autocomplete:** Via RediSearch module or sorted sets.
8. **Distributed Locks / Semaphores:** `SET NX EX` or Redlock for locking.
9. **Job Queues:** Lists (`LPUSH`/`RPOP`) or Streams (`XADD`, `XREADGROUP`) for producer/consumer patterns.
10. **Primary DB for Ephemeral Data:** Sub-millisecond access for ephemeral or session-related data.

---

## 21. Can you tell me more about Redis?

**Answer:**

* **In-Memory Storage:** All data in RAM; sub-millisecond latencies.
* **Data Structures:** Strings, Hashes, Lists, Sets, Sorted Sets, Bitmaps, HyperLogLog, Geospatial, Streams.
* **Persistence:**

  * **RDB Snapshots:** Periodic point-in-time dumps.
  * **AOF:** Logs every write command; can be replayed on restart.
* **Replication & HA:**

  * Master–Replica replication; Sentinel for automatic failover; Redis Cluster for sharding and failover across multiple nodes.
* **Eviction Policies:** Configurable `maxmemory` with LRU, LFU, TTL eviction strategies.
* **Modules:** RediSearch (full-text), RedisJSON, RedisGraph, RedisTimeSeries.
* **Clients:** Official drivers for Java (Jedis, Lettuce), Python (redis-py), Node.js (ioredis), etc.
* **Security:** AUTH, ACLs (Redis 6+), TLS/SSL support.
* **Monitoring:** `INFO` command, `MONITOR`, CLI tools (`redis-cli --stat`, `--latency`).

---

## 22. What are Cassandra’s data types?

**Answer:**

1. **Primitive Types:**

   * **Numeric:** `ascii`, `bigint`, `varint`, `int`, `smallint`, `tinyint`, `float`, `double`, `decimal`, `counter`.
   * **Textual:** `text`, `varchar`, `blob`.
   * **Temporal:** `timestamp`, `date`, `time`, `timeuuid`.
   * **Boolean & UUID:** `boolean`, `uuid`, `timeuuid`.
   * **Inet & Duration:** `inet`, `duration`.
2. **Collection Types:**

   * `list<element_type>`, `set<element_type>`, `map<key_type, value_type>`.
3. **User-Defined Types (UDTs):**

   * Custom composite types defined with `CREATE TYPE …`. Use `frozen<UDT>` when storing in collections.
4. **Tuple Types:**

   * Fixed-length ordered collections of heterogeneous types (e.g., `tuple<double, double>` for coordinates).
5. **Counter & Duration Types:**

   * `counter` for distributed increment/decrement.
   * `duration` for storing a period (months, days, nanoseconds).

---

## 23. What are a memtable and an SSTable in Cassandra?

**Answer:**

1. **Memtable:**

   * In-memory write-back cache for newly written data.
   * On write: data is appended to the Commit Log (durability) and inserted into the memtable.
   * When memtable reaches a size threshold, it is flushed to disk as an SSTable.
2. **SSTable (Sorted String Table):**

   * Immutable on-disk files storing sorted key–value pairs.
   * Consists of:

     * **Data file (`.db`):** Serialized rows.
     * **Partition index (`.idx`):** Maps partition keys to offsets in the data file.
     * **Bloom filter (`.filter`):** Probabilistic structure to test key existence.
     * **Compression info (`.compress`):** Metadata for block decompression.
     * **Statistics (`.stats`):** SSTable metadata (row count, tombstones, timestamps).
   * Multiple SSTables accumulate; compaction merges SSTables, discards tombstones, and keeps data sorted.
   * **Read Path:** Query memtable → check Bloom filters for SSTables → read from SSTables if Bloom filter is positive → merge results (memtable + SSTables) and apply tombstone logic.

---

## 24. What do “wide column” and “column family” mean (in Cassandra)?

**Answer:**

1. **Column Family:**

   * Equivalent to a table in RDBMS. A container for rows keyed by a partition key. Tables are backed by column families internally.
   * Defined in CQL as `CREATE TABLE …`.
2. **Wide Column:**

   * Each row (partition key) can have a large, dynamic set of columns.
   * Columns within a partition are sorted by clustering keys.
   * Example:

     ```cql
     CREATE TABLE user_events (
       user_id UUID,
       event_time timestamp,
       event_type text,
       PRIMARY KEY (user_id, event_time)
     ) WITH CLUSTERING ORDER BY (event_time DESC);
     ```
   * All events for one `user_id` form a “wide row,” sorted by `event_time`.

---

## 25. What is a partition key (in Cassandra)?

**Answer:**

* The partition key determines how data is distributed across nodes.
* Part of the primary key enclosed in the first set of parentheses:

  ```cql
  PRIMARY KEY ((partition_key), clustering_key1, clustering_key2, …)
  ```
* The partition key is hashed to generate a token; the token maps the row to one or more nodes (replicas).
* Rows with the same partition key reside on the same node(s).
* Good partition key design: even data distribution, avoid hotspots, keep partition size manageable.

---

## 26. What is a clustering key (in Cassandra)?

**Answer:**

* The clustering key follows the partition key in the primary key definition.
* It determines the sort order of rows within a partition and ensures uniqueness in combination with the partition key.
* Example:

  ```cql
  CREATE TABLE user_posts (
    user_id UUID,
    post_time timestamp,
    post_id UUID,
    content text,
    PRIMARY KEY (user_id, post_time, post_id)
  ) WITH CLUSTERING ORDER BY (post_time DESC, post_id ASC);
  ```
* `post_time` and `post_id` are clustering keys; rows for a `user_id` partition are sorted by `post_time DESC`.
* Clustering keys enable efficient range scans within a partition (e.g., posts between two timestamps).

---

## 27. What is a graph database?

**Answer:**

* A graph database is a NoSQL database designed to store and query data modeled as nodes (entities) and edges (relationships).
* Emphasizes relationships and traversals, allowing efficient querying of complex, connected data (e.g., social networks, recommendation engines).
* Data Model:

  * **Nodes:** Entities with properties.
  * **Edges:** Directed or undirected relationships between nodes, also with properties.
  * **Properties:** Key–value pairs attached to nodes or edges.
* Query Languages:

  * **Cypher:** Used by Neo4j.
  * **Gremlin:** TinkerPop graph traversal language (supports AWS Neptune, JanusGraph).
  * **GQL:** Upcoming ISO standard.
* Use Cases: Fraud detection, knowledge graphs, social network analysis, recommendation systems, network/IT operations.

---

## 28. How many ways to store a graph (in LeetCode)?

**Answer:**
Common graph representations in LeetCode and algorithm problems:

1. **Adjacency List:**

   * For each node, store a list (array or linked list) of neighboring nodes.
   * Memory: O(V + E).
   * Good for sparse graphs.
   * Example in code:

     ```python
     graph = {
       0: [1, 2],
       1: [0, 3],
       2: [0],
       3: [1]
     }
     ```
2. **Adjacency Matrix:**

   * A 2D array `adj[V][V]` where `adj[i][j] = 1` if edge (i, j) exists, else 0.
   * Memory: O(V²).
   * Good for dense graphs or when quick edge existence check is needed.
3. **Edge List:**

   * A list of edges, each represented as a pair or tuple `(u, v)`.
   * Memory: O(E).
   * Requires scanning the list to find neighbors; less efficient for traversal.
4. **Adjacency Set (Hash-Based):**

   * Similar to adjacency list, but neighbors stored in a hash set for O(1) existence checks.
   * Useful when you need frequent edge existence queries.

---

## 29. What is a primary key?

**Answer:**

* A primary key is a column (or combination of columns) in a relational table that uniquely identifies each row.
* Characteristics:

  1. **Uniqueness:** No two rows have the same primary key value.
  2. **Non-null:** Primary key columns cannot be NULL.
* Enforced by creating an index (clustered or unique) on the primary key.
* Example:

  ```sql
  CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    username VARCHAR(50) NOT NULL
  );
  ```

---

## 30. What is a foreign key?

**Answer:**

* A foreign key is a column (or set of columns) in one table that references the primary key of another table. It enforces referential integrity between the two tables.
* Characteristics:

  1. **Referential Link:** Ensures that the value in the foreign key column exists in the referenced primary key column of the parent table.
  2. **ON DELETE / ON UPDATE Actions:** You can specify `CASCADE`, `SET NULL`, `RESTRICT`, `NO ACTION`, etc.
* Example:

  ```sql
  CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    user_id INT NOT NULL,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
      ON DELETE CASCADE
  );
  ```

---

## 31. What is database normalization?

**Answer:**

* Database normalization is the process of organizing tables and columns to reduce redundancy and avoid anomalies (insertion, update, and deletion anomalies).
* **Normal Forms:**

  1. **1NF (First Normal Form):** Each column contains atomic (indivisible) values; no repeating groups or arrays.
  2. **2NF (Second Normal Form):** In 1NF, and every non-primary-key column depends on the entire primary key (no partial dependencies).
  3. **3NF (Third Normal Form):** In 2NF, and no transitive dependencies (no non-key column depends on another non-key column).
  4. **BCNF (Boyce–Codd Normal Form):** Every determinant is a candidate key (stronger than 3NF).
  5. **4NF, 5NF, etc.:** Address multi-valued dependencies and join dependencies, less common in OLTP systems.
* **Benefits:** Eliminates redundant data, enforces data integrity, simplifies updates.
* **Trade-offs:** Over-normalization may lead to excessive JOINs and degrade performance; sometimes controlled denormalization is used for read-heavy workloads.

---

## 32. What is a transaction?

**Answer:**

* A transaction is a logical unit of work comprising one or more database operations (INSERT, UPDATE, DELETE, SELECT) that must either all succeed (commit) or all fail (rollback).
* **ACID Properties:**

  1. **Atomicity:** All or nothing.
  2. **Consistency:** Database moves from one valid state to another.
  3. **Isolation:** Concurrent transactions do not interfere.
  4. **Durability:** Committed data persists despite failures.
* Transactions ensure data integrity and consistency, especially in multi-user environments.
* Example:

  ```sql
  START TRANSACTION;
    UPDATE accounts SET balance = balance - 100 WHERE id = 1;
    UPDATE accounts SET balance = balance + 100 WHERE id = 2;
  COMMIT;
  ```

---

## 33. What is ORM?

**Answer:**

* **ORM (Object-Relational Mapping):** A programming technique for converting data between incompatible systems (object-oriented languages and relational databases).
* **Purpose:** Map classes in application code to database tables, and objects to rows, so developers can interact with the database using objects rather than SQL.
* **Features:**

  1. **Entity Mapping:** Define how classes/fields map to tables/columns (via annotations or XML).
  2. **CRUD Operations:** Abstract CRUD as methods on objects (e.g., `session.save(entity)`).
  3. **Lazy/Eager Loading:** Control when related entities are fetched.
  4. **Caching:** First-level (session) and second-level (shared) caching to improve performance.
  5. **Transaction Management:** Integrate with transactions seamlessly.
* **Examples:** Hibernate (Java), Entity Framework (C#), SQLAlchemy (Python), ActiveRecord (Ruby).

---

## 34. What is JPA?

**Answer:**

* **JPA (Java Persistence API):** A Java specification (JSR 338) defining a standard for ORM in Java.
* **Purpose:** Provide a uniform set of interfaces and annotations to map Java objects to relational tables and perform CRUD operations without writing boilerplate JDBC code.
* **Core Components:**

  1. **Entity:** A plain Java class annotated with `@Entity`, representing a database table.
  2. **EntityManager:** The primary interface for CRUD operations, JPQL queries, and transaction management.
  3. **Persistence Unit:** Defined in `persistence.xml` (or via Spring Boot auto-configuration).
  4. **Annotations:**

     * `@Entity`, `@Table`, `@Id`, `@GeneratedValue`, `@Column`, `@OneToMany`, `@ManyToOne`, etc.
* **Implementations:** Hibernate (most popular), EclipseLink, OpenJPA, DataNucleus.
* **JPQL:** Java Persistence Query Language, an object-oriented query language similar to SQL but operating on entities.

---

## 35. What are the three major areas of persistence in JPA?

**Answer:**

1. **Entity Mapping & Metadata:**

   * Annotations or XML descriptors (`@Entity`, `@Table`, `@Column`, `@Id`, `@GeneratedValue`, associations like `@OneToMany`, `@ManyToOne`).
   * Defines how classes and fields map to relational tables and columns.
2. **Entity Lifecycle & State Transitions:**

   * **New/Transient:** Instance created, not managed or persisted.
   * **Managed/Persistent:** Instance is managed by `EntityManager`, changes tracked and synchronized on `flush` or `commit`.
   * **Detached:** Previously persistent instance whose persistence context is closed or cleared; can be merged back.
   * **Removed:** Instance scheduled for deletion on `commit`.
   * **Operations:** `persist()`, `merge()`, `remove()`, `detach()`, `flush()`, `refresh()`.
3. **Querying & Transaction Management:**

   * **JPQL / Criteria API:** Object-oriented queries over entities (`EntityManager.createQuery("SELECT u FROM User u WHERE u.age > :age")`).
   * **Named Queries:** Predefined JPQL queries via `@NamedQuery`.
   * **Native SQL Queries:** `entityManager.createNativeQuery(...)` for vendor-specific SQL.
   * **Transactions:** Using `EntityTransaction` in Java SE or container-managed transactions (`@Transactional`) in Java EE/Spring.

---

## 36. What is Hibernate?

**Answer:**

* **Hibernate:** A leading open-source ORM framework for Java, implementing the JPA specification and providing native features beyond JPA.
* **Features:**

  1. **Entity Mapping:** Annotations or XML to map Java classes to database tables.
  2. **Session & SessionFactory:** Core interfaces for interacting with the database (`SessionFactory` is thread-safe; `Session` represents a unit of work).
  3. **HQL (Hibernate Query Language):** Object-oriented query language similar to JPQL with additional features.
  4. **Criteria API:** Programmatic way to build queries in a type-safe manner.
  5. **Caching:** First-level (Session) and second-level (EHCache, Infinispan, etc.).
  6. **Lazy/Eager Fetching:** Control when associated entities are loaded.
  7. **Automatic Schema Generation:** Can generate DDL from mappings (`hbm2ddl.auto`).
* **Extensions:** Support for user-defined types, custom identifier generators, interceptors, event listeners, multi-tenancy.

---

## 37. In Hibernate, what does the `<generator>` element do?

**Answer:**

* In Hibernate XML mapping (`.hbm.xml`), the `<generator>` element (inside `<id>`) specifies how primary key values are generated for an entity.
* **Common Strategies:**

  1. `<generator class="native"/>`: Chooses identity, sequence, or hilo depending on dialect.
  2. `<generator class="identity"/>`: Uses auto-increment/identity column in DB (e.g., MySQL `AUTO_INCREMENT`).
  3. `<generator class="sequence">` `<param name="sequence_name">seq_name</param>` `</generator>`: Uses a database sequence (e.g., Oracle, PostgreSQL).
  4. `<generator class="increment"/>`: Uses a table in memory to increment values (not safe for clustered environments).
  5. `<generator class="uuid"/>` or `"uuid2"`: Generates UUID key as string.
* **Example:**

  ```xml
  <id name="id" column="user_id">
    <generator class="sequence">
      <param name="sequence_name">user_seq</param>
    </generator>
  </id>
  ```
* In annotation-based mapping (`@Id`), the equivalent is:

  ```java
  @Id
  @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "user_seq_gen")
  @SequenceGenerator(name = "user_seq_gen", sequenceName = "user_seq")
  private Long id;
  ```

---

## 38. What is a derived property in Hibernate?

**Answer:**

* A **derived property** is a class-level property that is not directly mapped to a database column but is computed from one or more mapped properties.
* Typically implemented using the `@Formula` annotation or via a transient getter method.
* **`@Formula`:** Executes a SQL fragment when loading the entity and maps the result to a property.

  ```java
  @Entity
  public class Order {
      @Id
      private Long id;

      private BigDecimal unitPrice;
      private Integer quantity;

      @Formula("unit_price * quantity")
      private BigDecimal totalPrice;

      // getters/setters
  }
  ```
* **Transient Getter Method:** Compute the value in Java without persisting.

  ```java
  @Entity
  public class Order {
      @Id
      private Long id;

      private BigDecimal unitPrice;
      private Integer quantity;

      @Transient
      public BigDecimal getTotalPrice() {
          return unitPrice.multiply(BigDecimal.valueOf(quantity));
      }

      // getters/setters
  }
  ```

---

## 39. What are the core interfaces of Hibernate?

**Answer:**

1. **SessionFactory:**

   * Thread-safe, heavyweight object created once per application.
   * Configures Hibernate, provides `Session` instances, caches metadata, manages connection pools (via `ServiceRegistry`).
2. **Session:**

   * Lightweight, non-thread-safe object representing a single unit of work.
   * Maintains first-level cache, tracks entity state, handles CRUD operations.
   * Associated with a transaction (`Transaction` interface).
3. **Transaction:**

   * Manages transaction boundaries (begin, commit, rollback).
   * In Java SE, you obtain via `session.beginTransaction()`. In Java EE/Spring, container-managed or `@Transactional` is preferred.
4. **Query / CriteriaQuery:**

   * `org.hibernate.query.Query` for HQL/JPQL/native SQL queries.
   * JPA Criteria API (`CriteriaBuilder`, `CriteriaQuery`) for type-safe, programmatic queries.
5. **Interceptor / Event Listener:**

   * Hooks to intercept or listen to persistence events (e.g., `onSave`, `onUpdate`, `preFlush`, `postLoad`).
6. **Configuration / Metadata:**

   * `org.hibernate.cfg.Configuration` (legacy) or `org.hibernate.boot.Metadata` (modern) used to build `SessionFactory`.

---

