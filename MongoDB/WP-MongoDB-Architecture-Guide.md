# Abstract of White Paper - MongoDB Architecture Guide
> Get this paper at: [mongodb-architecture-guide](https://www.mongodb.com/collateral/mongodb-architecture-guide). Added additional details for certain points from www.mongodb.com


## Need / Characterstics / Features:
* Allows **dynamic modification of the schema** without downtime or performance impact.
* **Scalability ability and Performance**. performance with sharding or partitioning; scaled out across commodity hardware deployed on-premises or in the cloud.
* NoSQL databases are designed for **continuously available** systems
* Allows **data be represented** as simple key-value pairs and flat, table-like structures, through to rich documents and objects with deeply nested arrays and sub-documents
* **expressive query language**, simple lookup, faceted search, JOINs and graph traversals. Supports, `equi` and `non-equi` JOINs that combine data from multiple collections.
* Allows users to **mix and match multiple storage engines** within a single deployment. This flexibility provides a more simple and reliable approach to meeting diverse application needs for data. Traditionally, multiple database technologies would need to be managed to meet these needs, with complex, custom integration code to move data between the technologies, and to ensure consistent, secure access.


---

### MongoDB Multimodel Architecture:
- MongoDB 3.4 ships with four supported *storage engines*, all of which can coexist within a single MongoDB replica set:
    1. The default **WiredTiger** storage engine; granular concurrency control and native compression.
    2. The **Encrypted storage** engine protecting highly sensitive data (EEdition)
    3. The **In-Memory** storage engine delivering the extreme performance coupled with real time analytics for the most demanding, latency-sensitive applications. (EEdition), Percona as alternative.
    4. **MMAPv1** used in pre-3.x MongoDB releases.

### MongoDB Data Model
- **Data As Documents:** BSON encoding extends the popular JSON to include additional types such as int, long, date, floating point, and decimal128. Documents that tend to share a similar structure are organized as collections
- **Dynamic Schema without Compromising Data Governance:** Fields can vary from document to document; there is no need to declare the structure of documents to the system – documents are self describing.
- **Document Validation:** Unlike NoSQL databases that push enforcement of these controls back into application code, MongoDB provides document validation within the database. Users can enforce checks on document structure, data types, data ranges and the presence of mandatory fields.
- **Schema Design:** Although MongoDB provides schema flexibility, schema design is still important
- **[Data Modelling](https://docs.mongodb.com/manual/data-modeling/):** The key challenge in data modeling is balancing the needs of the application, the performance characteristics of the database engine, and the data retrieval patterns. When designing data models, always consider the application usage of the data (i.e. queries, updates, and processing of the data) as well as the inherent structure of the data itself.
    + The key decision in designing data models for MongoDB applications revolves around the structure of documents and how the application represents relationships between data. There are two tools that allow applications to represent these relationships:
        1. **References:** References store the relationships between data by including links or references from one document to another. Follow **normalized data model**. MongoDB applications use one of two methods for relating documents:
            1. **Manual references:** where you save the _id field of one document in another document as a reference. Then your application can run a second query to return the related data. The only limitation of manual linking is that these references do not convey the database and collection names. If you have documents in a single collection that relate to documents in more than one collection, you may need to consider using DBRefs.
            2. **DBRefs:** are references from one document to another using the value of the first document’s _id field, collection name, and, optionally, its database name. 
        2. **Embedded Documents:** MongoDB documents make it possible to embed document structures in a field or array within a document. These **denormalized** data models allow applications to retrieve and manipulate related data in a single database operation.
    + Decision Factors:
        + Document Growth: Careful in MMAPv1 engine
        + Atomicity of Write Operations
        + Data Use and Performance
        + Sharding
        + Indexes:
            * As you create indexes, consider the following behaviors of indexes:
                * Each index requires at least 8 kB of data space.
                * Adding an index has some negative performance impact for write operations. For collections with high write-to-read ratio, indexes are expensive since each insert must also update any indexes.
                * Collections with high read-to-write ratio often benefit from additional indexes. Indexes do not affect un-indexed read operations.
                * When active, each index consumes disk space and memory. This usage can be significant and should be tracked for capacity planning, especially for concerns over working set size.
        + Large Number of Collections:
            * Generally, having a large number of collections has no significant performance penalty and results in very good performance. Distinct collections are very important for high-throughput batch processing.
        + Collection Contains Large Number of Small Documents
        + Storage Optimization for Small Documents:
            * Use the `_id` field explicitly.
            * Use shorter field names
        + Data Lifecycle Management  

### MongoDB Query Model
- **Idiomatic Drivers:** One fundamental difference with relational databases is that the MongoDB query model is implemented as methods or functions within the API of a specific programming language, as opposed to a completely separate language like SQL.
- **Interacting with the Database:** 1. mongo shell 2. MongoDB Compass
- **Querying and Visualizing Data:** MongoDB is not limited to simple Key-Value operations. Developers can build rich applications using complex queries, aggregations and secondary indexes that unlock the value in structured, semi-structured and unstructured data:
    1. Key-value queries
    2. Range queries
    3. Geospatial queries
    4. Search
    5. Aggregation Framework
    6. JOINs and graph traversals: Through the $lookup stage of the aggregation pipeline, documents from separate collections can be combined through a left outer JOIN operation.
    7. MapReduce queries: expressed in JavaScript and executed across database.
- Additionally the MongoDB Connector for Apache Spark exposes Spark’s Scala, Java, Python, and R libraries.
- **Data Visualization with BI Tools**: Available in EEdition.
- **Indexing:** By default, the WiredTiger storage engine compresses indexes in RAM, freeing up more of the working set for documents. Support for many types of secondary indexes that can be declared on any field in the document, including fields within arrays:
    1. **Unique Indexes**: By specifying an index as unique, MongoDB will reject inserts of new documents or the update of a document with an existing value for the field for which the unique index has been created.  If a compound index is specified as unique, the combination of values must be unique.
    2. **Compound Indexes**: It can be useful to create compound indexes for queries that specify multiple predicates. An additional benefit of a compound index is that any leading field within the index can be used, so fewer indexes on single fields may be necessary.
    3. **Array Indexes**: For fields that contain an array, each array value is stored as a separate index entry. There is no special syntax required for creating array indexes – if the field contains an array, it will be indexed as a array index.
    4. **TTL Indexes**: In some cases data should expire out of the system automatically. Time to Live (TTL) indexes allow the user to specify a period of time after which the data will automatically be deleted from the database.
    5. **Geospatial Indexes**
    6. **Partial Indexes**: By specifying a filtering expression during index creation, a user can instruct MongoDB to include only documents that meet the desired conditions, for example by only indexing active customers. Partial indexes balance delivering low latency query performance while reducing system overhead.
    7. **Sparse Indexes**: Sparse indexes only contain entries for documents that contain the specified field. Sparse indexes allow for smaller, more efficient indexes when fields are not present in all documents.
    8. **Text Search Indexes**: MongoDB provides a specialized index for text search that uses advanced, language-specific linguistic rules for stemming, tokenization, case sensitivity and stop words. Queries that use the text search index will return documents in relevance order. One or more fields can be included in the text index.
- **Query Optimization:**
    + Automatic, based on *predicates* and *sort* inputs.
    + selects the best index to use by periodically running alternate query plans and selecting the index with the best response time for each query type.
    + The results of this empirical test are stored as a cached query plan and are updated periodically
- **Covered Queries:** Queries that return results containing only indexed fields are called covered queries

### MongoDB Data Management
- **Sharding:** Sharding is a method for distributing data across multiple machines. MongoDB uses sharding to support deployments with very large data sets and high throughput operations. There are two methods for addressing system growth: vertical and horizontal scaling.
    + **Vertical Scaling**: involves increasing the capacity of a single server, such as using a more powerful CPU, adding more RAM, or increasing the amount of storage space.
    + **Horizontal Scaling**: involves dividing the system dataset and load over multiple servers, adding additional servers to increase capacity as required. each machine handles a subset of the overall workload, potentially providing better efficiency than a single high-speed high-capacity server. The trade off is increased complexity in infrastructure and maintenance for the deployment.
    + *MongoDB supports horizontal scaling through sharding.*
    + Automatic sharding provides horizontal scalability in MongoDB.
    + **Sharded Cluster** A MongoDB sharded cluster consists of the following components:
        1. **shard**: Each shard contains a subset of the sharded data. Each shard can be deployed as a replica set.
        2. **mongos**: The mongos acts as a query router, providing an interface between client applications and the sharded cluster.
        3. **config servers**: Config servers store metadata and configuration settings for the cluster.
    + MongoDB shards data at the collection level, distributing the collection data across the shards in the cluster.
    + **Shard Keys:** To distribute the documents in a collection, MongoDB partitions the collection using the shard key. The shard key consists of an immutable field or fields that exist in every document in the target collection. The choice of shard key cannot be changed after sharding. A sharded collection can have only one shard key.
    + **Chunks:** MongoDB partitions sharded data into chunks. Each chunk has an inclusive lower and exclusive upper range based on the shard key. MongoDB migrates chunks across the shards in the sharded cluster using the sharded cluster balancer.
    + **Advantages of Sharding:**
        * Reads / Writes
        * Storage Capacity
        * High Availability
    + multiple sharding policies are available:
        1. Range Sharding
        2. Hash Sharding
        3. Zone Sharding: Zones accommodate a range of deployment scenarios – for example locating data by geographic region, by hardware configuration for tiered storage architectures, or by application feature.
- **Query Router:** whether there is one or one hundred shards, the application code for querying MongoDB is the same. Applications issue queries to a query router that dispatches the query to the appropriate shards.
    + Multiple query routers can be used with a MongoDB system

### Consistency
- **Transaction Model & Configurable Write Availability:** 
    + MongoDB is ACID compliant at the document level.
    + The ACID guarantees provided by MongoDB ensures complete isolation as a document is updated; any errors cause the operation to roll back and clients receive a consistent view of the document.
    + MongoDB also allows users to specify write availability in the system using an option called the *write concern*.
    + Developers can use MongoDB's Write Concerns to configure operations to commit to the application only after specific policies have been fulfilled – for example only after the operation has been flushed to the journal on disk. Each query can specify the appropriate write concern, ranging from unacknowledged to acknowledgement that writes have been committed to all replicas.

### Availability
- **Replication:** MongoDB maintains multiple copies of data called replica sets using native replication.
    + A replica set is a group of mongod instances that maintain the same data set. A replica set contains several data bearing nodes and optionally one arbiter node. Of the data bearing nodes, one and only one member is deemed the primary node, while the other nodes are deemed secondary nodes.
    + A replica set is a fully self-healing shard that helps prevent database downtime and can be used to scale read operations. Replica failover is fully automated.
    + The number of replicas in a MongoDB replica set is configurable (max 50).
- **Replica Set Oplog:** Operations that modify a database on the primary replica set member are replicated to the secondary members using the oplog (operations log).
    + The oplog contains an ordered set of idempotent operations that are replayed on the secondaries
    + size: 5% of disk
    + Replica set can be recovered from *oplog* or *initial synchronization*.
- **Elections And Failover:** If the primary replica for a shard fails, secondary replicas together determine which replica should be elected as the new primary using an extended implementation of the *Raft consensus algorithm*.
- **Election Priority:** Sophisticated algorithms control the replica set election process, ensuring only the most suitable secondary member is promoted to primary, and reducing the risk of unnecessary failovers (also known as "false positives").

### Performance & Compression
- **In-Memory Performance With On-Disk Capacity:** MongoDB replica sets allow for hybrid in-memory and on-disk database deployments.
- **Storage & Network Efficiency with Compression:** The WiredTiger and Encrypted storage engines support native compression, reducing physical storage footprint by as much as 80%.
    - In addition to reduced storage space, compression enables much higher storage I/O scalability as fewer bits are read from disk. Administrators have the flexibility to configure specific compression algorithms for collections, indexes and the journal, choosing between:
        1. Snappy (default)
        2. zlib
        3. Prefix compression for indexes
    - can modify the default compression settings for all collections and indexes. Compression is also configurable on a per-collection and per-index basis during collection and index creation.
    - In addition to storage, MongoDB also offers intra-cluster network compression. Based on the snappy compression algorithm, network traffic can be compressed by up to 70%, providing major performance benefits in bandwidth-constrained environments, and reducing networking costs.

### Security
- MongoDB Enterprise Advanced features extensive capabilities to defend, detect, and control access to data:
    + Authentication
    + Authorization
    + Auditing: using MongoDB's native audit log.
    + Encryption

### Managing MongoDB - Provisioning, Monitoring and Disaster Recovery
- **MongoDB Ops Manager**: is the simplest way to run MongoDB in your own environment, making it easy for operations teams to deploy, monitor, backup and scale MongoDB.
- **Deployments and Upgrades**
- **Monitoring:** Ops Manager and Cloud Manager have been developed to give administrators the insights
- **Disaster Recovery: Backups & Point-in-Time Recovery**
- **SNMP: Integrating MongoDB with External Monitoring Solutions**
- **MongoDB Atlas: Database as a Service For MongoDB**
