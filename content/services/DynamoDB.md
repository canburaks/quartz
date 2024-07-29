---
tags:
  - dynamodb
---
## DynamoDB Features
- A mix of `key-value` and `document` database.
- The value can be #string, #number, #boolean, #binary, #list, #json
- Provides #high-throughput and single-digit latency
- By Replicates data across multiple #az 
- Permissions are handles by [[IAM]] service. No need to provide a db-endpoint, username or password.
- Core components
	- Table
		- The name must be unique for the base table it is associated with, but you can use the same name for indexes that are associated with different base tables.
	- Item
	- Attribute
- Optimistic locking [🔗](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBMapper.OptimisticLocking.html) ( #java  and #dotnet )
	- Optimistic locking is a strategy to ensure that the client-side item that you are updating (or deleting) is the same as the item in Amazon DynamoDB
	- It allows only update an item if the version on the server was not changed. If another user updates it before you, just retrieve the item with a fresh #version and then trying to update it.
- Partitions
	- Hot partitions
		- A hot partition occurs when a disproportionate amount of traffic is directed toward a single partition. 
		- Results in
			- Throttling
			- Latency
			- Uneven performance
	- Cold partitions
		- Opposite of hot partitions.
		- Occurs when a partition is under utilized.


https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html‬
https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html‬
‭
---

## Primary and Secondary Keys
### Primary Keys
- Partition key
	- unique value of an item in a table
- Composite Key (Partition Key + Sort Key)
	- Partition Key
	- Sort key

### Secondary Keys
- Every secondary index is associated with exactly one table, from which it obtains its data
- You also define the attributes that you want to be projected, or copied, from the base table into the index. DynamoDB copies these attributes into the index, along with the primary key attributes from the base table. You can then query or scan the index just as you would query or scan a table.
- Types of secondary indexes
	- `Local Secondary Index (LSI)`
		- A local secondary index uses the same partition key as defined on the table, but a different attribute as the sort key
		- Can only be created during the table creation
		- Support both eventual and strong consistency
		- An index that has the same partition key as the base table, but a different sort key. A local secondary index is "local" in the sense that every partition of a local secondary index is scoped to a base table partition that has the same partition key value
		- The partition key must be the same as the base table's partition key, and the sort key must be a non-key base table attribute.
	- `Global Secondary Index (GSI)`
		- Can be created and deleted any time
		- Supports only eventual consistency
		- A global secondary index is considered "global" because queries on the index can span all of the data in the base table, across all partitions. 
		- A global secondary index is stored in its own partition space away from the base table and scales separately from the base table. (Different provisioned throughput)
		- Can be any scalar attribute of the base table. A sort key is optional, and it too can be any scalar attribute of the base table.

https://aws.amazon.com/blogs/database/how-to-design-amazon-dynamodb-global-secondary-indexes
https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/SecondaryIndexes.html
https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/LSI.html

---
## Projections
- When creating an LSI or GSI you set the list of attributes that you want to project or copy from the base table to the `secondary index`. 
- The primary keys (`partition` and `sort` key) are always projected into the `secondary index`, but you can also specify non-key attributes that will be projected into the index.
- Three options in projecting attributes:
	 1. KEYS_ONLY - all items of an index will only contain the base table’s primary keys that you set.
	 2. INCLUDE - allow you to choose other non-key attributes that will be included along with the primary keys of your base table.
	 3. ALL - all attributes from your base table will be copied into your secondary index.


 https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html

## Query & Scan
- 🍃 Query
	- Performs a direct look-up to specific items you want to search for based on a partition key
	- More performant than Scan operations, e
- 🍂 Scan
	- Scan operation will literally read and return every item in a table
	- Optionally provide a filter expression when requesting a Scan to return only a subset of items in the‬‭ table. However, the filtering occurs only after the scan is completed. In effect, you will still be charged for all‬‭ items read


## Read & Write Capacity
- Read Capacity
	- On-Demand Mode
		- DynamoDB charges you for the reads that your application performs on your tables in terms of read‬ ‭ request units
		- 1 Read Request Unit (RRU) equals
			- 4KB/s strongly consistent
			- 8KB/s eventually consistent
	- Provisioned Mode
		- We specify the number of reads and writes per second that you require for‬‭ your application.
		- Can use auto-scaling to adjust your table’s provisioned capacity automatically in response‬‭ to traffic changes
- Write Capacity
	- On-Demand Mode
		- 1 Write Request Unit (WRU) equals to 1 write of up to 1 KB/s

https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/capacity-mode.html

---

## DynamoDB Streams
- Captures item-level changes in a DynamoDB table in near-real-time
-  When‬‭ an item is modified using any of the write operations (‬‭`PutItem`, `UpdateItem`, or `DeleteItem`‬‭), DynamoDB detects it as an event and sends the modified record to a transaction log.
- Retained for 24 hours
- Orders are preserved
- Can be used for
	- Aggregation of data for auditing
	- Notification
- The Stream View Type determines what kind of data you want to capture.
	 1. `KEYS_ONLY` - only the key attributes (Partition Key + Sort Key) of the modified item are captured.
	 2. `NEW_IMAGE` - the latest state of the entire modified item will be captured.
	 3. `OLD_IMAGES` - the entire item as it appeared prior to the update is captured.
	 4. `NEW_AND_OLD_IMAGES` - both the latest and the previous state prior to the update is captured.

>[!tip]
> If you need a more complex streaming application, you might want to configure DynamoDB to send records to‬
‭ a Kinesis Data stream.

- https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html
- https://aws.amazon.com/blogs/database/dynamodb-streams-use-cases-and-design-patterns/
- https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/kds.html

## DynamoDB Accelerator (DAX)
- In-memory cache that is purposely built for DynamoDB
- Response times in microseconds for millions of requests per second
- Doesn't support strong consistency
- Use DAX
	1. You have a read-intensive application that requires response time in the microsecond range.
	2. You have a large set of data that are frequently read.
	3. Your application can eventually tolerate consistent reads.
- Don't use DAX
	1. Your application requires strongly consistent reads.
	2. Your application performs more writes than reads.

- https://aws.amazon.com/dynamodb/global-tables/
- https://aws.amazon.com/blogs/database/how-to-use-amazon-dynamodb-global-tables-to-power-multiregion-architectures/


---

## SDK Examples
- Request parameters:
	- ``ReturnConsumedCapacity
		- `TOTAL`: Total number of the capacity consumed
		- `INDEXES`: Total number of capacity consumed  with subtotals for base and secondary indexes affected table.
		- `NONE`: No return
- `PutItem` [🔗](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html#API_PutItem_RequestParameters)
- `UpdateItem` [🔗](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_UpdateItem.html) 
		- 
		- Atomic counters
				- Not an idempotent action
				- Can bu used to track number of visitors
				- Not a solution for apps that overcounter/undercounter sensitive
		- `UpdateExpression`
			- `SET` - Adds one or more attributes and values to an item. If any of these attributes already exist, they are replaced by the new values. You can also use `SET` to add or subtract from an attribute that is of type Number. For example: `SET myNum = myNum + :val`. Supports the following functions:
				- `if_not_exists` (path, operand) - if the item does not contain an attribute at the specified path, then if_not_exists evaluates to operand; otherwise, it evaluates to path. You can use this function to avoid overwriting an attribute that may already be present in the item.
				- `list_append` (operand, operand) - evaluates to a list with a new element added to it. You can append the new element to the start or the end of the list by reversing the order of the operands.
			- `REMOVE` - Removes one or more attributes from an item.
			- `ADD` - Adds the specified value to the item, if the attribute does not already exist.
		- `ReturnValue`
			- `NONE` - If ReturnValues is not specified, or if its value is NONE, then nothing is returned. (This setting is the default for ReturnValues.)
			- `ALL_OLD` - Returns all of the attributes of the item, as they appeared before the UpdateItem operation.
			- `UPDATED_OLD` - Returns only the updated attributes, as they appeared before the UpdateItem operation.
			- `ALL_NEW` - Returns all of the attributes of the item, as they appear after the UpdateItem operation.
			- `UPDATED_NEW` - Returns only the updated attributes, as they appear after the UpdateItem operation.



### AWS Reserved Words
```txt

ABORT
ABSOLUTE
ACTION
ADD
AFTER
AGENT
AGGREGATE
ALL
ALLOCATE
ALTER
ANALYZE
AND
ANY
ARCHIVE
ARE
ARRAY
AS
ASC
ASCII
ASENSITIVE
ASSERTION
ASYMMETRIC
AT
ATOMIC
ATTACH
ATTRIBUTE
AUTH
AUTHORIZATION
AUTHORIZE
AUTO
AVG
BACK
BACKUP
BASE
BATCH
BEFORE
BEGIN
BETWEEN
BIGINT
BINARY
BIT
BLOB
BLOCK
BOOLEAN
BOTH
BREADTH
BUCKET
BULK
BY
BYTE
CALL
CALLED
CALLING
CAPACITY
CASCADE
CASCADED
CASE
CAST
CATALOG
CHAR
CHARACTER
CHECK
CLASS
CLOB
CLOSE
CLUSTER
CLUSTERED
CLUSTERING
CLUSTERS
COALESCE
COLLATE
COLLATION
COLLECTION
COLUMN
COLUMNS
COMBINE
COMMENT
COMMIT
COMPACT
COMPILE
COMPRESS
CONDITION
CONFLICT
CONNECT
CONNECTION
CONSISTENCY
CONSISTENT
CONSTRAINT
CONSTRAINTS
CONSTRUCTOR
CONSUMED
CONTINUE
CONVERT
COPY
CORRESPONDING
COUNT
COUNTER
CREATE
CROSS
CUBE
CURRENT
CURSOR
CYCLE
DATA
DATABASE
DATE
DATETIME
DAY
DEALLOCATE
DEC
DECIMAL
DECLARE
DEFAULT
DEFERRABLE
DEFERRED
DEFINE
DEFINED
DEFINITION
DELETE
DELIMITED
DEPTH
DEREF
DESC
DESCRIBE
DESCRIPTOR
DETACH
DETERMINISTIC
DIAGNOSTICS
DIRECTORIES
DISABLE
DISCONNECT
DISTINCT
DISTRIBUTE
DO
DOMAIN
DOUBLE
DROP
DUMP
DURATION
DYNAMIC
EACH
ELEMENT
ELSE
ELSEIF
EMPTY
ENABLE
END
EQUAL
EQUALS
ERROR
ESCAPE
ESCAPED
EVAL
EVALUATE
EXCEEDED
EXCEPT
EXCEPTION
EXCEPTIONS
EXCLUSIVE
EXEC
EXECUTE
EXISTS
EXIT
EXPLAIN
EXPLODE
EXPORT
EXPRESSION
EXTENDED
EXTERNAL
EXTRACT
FAIL
FALSE
FAMILY
FETCH
FIELDS
FILE
FILTER
FILTERING
FINAL
FINISH
FIRST
FIXED
FLATTERN
FLOAT
FOR
FORCE
FOREIGN
FORMAT
FORWARD
FOUND
FREE
FROM
FULL
FUNCTION
FUNCTIONS
GENERAL
GENERATE
GET
GLOB
GLOBAL
GO
GOTO
GRANT
GREATER
GROUP
GROUPING
HANDLER
HASH
HAVE
HAVING
HEAP
HIDDEN
HOLD
HOUR
IDENTIFIED
IDENTITY
IF
IGNORE
IMMEDIATE
IMPORT
IN
INCLUDING
INCLUSIVE
INCREMENT
INCREMENTAL
INDEX
INDEXED
INDEXES
INDICATOR
INFINITE
INITIALLY
INLINE
INNER
INNTER
INOUT
INPUT
INSENSITIVE
INSERT
INSTEAD
INT
INTEGER
INTERSECT
INTERVAL
INTO
INVALIDATE
IS
ISOLATION
ITEM
ITEMS
ITERATE
JOIN
KEY
KEYS
LAG
LANGUAGE
LARGE
LAST
LATERAL
LEAD
LEADING
LEAVE
LEFT
LENGTH
LESS
LEVEL
LIKE
LIMIT
LIMITED
LINES
LIST
LOAD
LOCAL
LOCALTIME
LOCALTIMESTAMP
LOCATION
LOCATOR
LOCK
LOCKS
LOG
LOGED
LONG
LOOP
LOWER
MAP
MATCH
MATERIALIZED
MAX
MAXLEN
MEMBER
MERGE
METHOD
METRICS
MIN
MINUS
MINUTE
MISSING
MOD
MODE
MODIFIES
MODIFY
MODULE
MONTH
MULTI
MULTISET
NAME
NAMES
NATIONAL
NATURAL
NCHAR
NCLOB
NEW
NEXT
NO
NONE
NOT
NULL
NULLIF
NUMBER
NUMERIC
OBJECT
OF
OFFLINE
OFFSET
OLD
ON
ONLINE
ONLY
OPAQUE
OPEN
OPERATOR
OPTION
OR
ORDER
ORDINALITY
OTHER
OTHERS
OUT
OUTER
OUTPUT
OVER
OVERLAPS
OVERRIDE
OWNER
PAD
PARALLEL
PARAMETER
PARAMETERS
PARTIAL
PARTITION
PARTITIONED
PARTITIONS
PATH
PERCENT
PERCENTILE
PERMISSION
PERMISSIONS
PIPE
PIPELINED
PLAN
POOL
POSITION
PRECISION
PREPARE
PRESERVE
PRIMARY
PRIOR
PRIVATE
PRIVILEGES
PROCEDURE
PROCESSED
PROJECT
PROJECTION
PROPERTY
PROVISIONING
PUBLIC
PUT
QUERY
QUIT
QUORUM
RAISE
RANDOM
RANGE
RANK
RAW
READ
READS
REAL
REBUILD
RECORD
RECURSIVE
REDUCE
REF
REFERENCE
REFERENCES
REFERENCING
REGEXP
REGION
REINDEX
RELATIVE
RELEASE
REMAINDER
RENAME
REPEAT
REPLACE
REQUEST
RESET
RESIGNAL
RESOURCE
RESPONSE
RESTORE
RESTRICT
RESULT
RETURN
RETURNING
RETURNS
REVERSE
REVOKE
RIGHT
ROLE
ROLES
ROLLBACK
ROLLUP
ROUTINE
ROW
ROWS
RULE
RULES
SAMPLE
SATISFIES
SAVE
SAVEPOINT
SCAN
SCHEMA
SCOPE
SCROLL
SEARCH
SECOND
SECTION
SEGMENT
SEGMENTS
SELECT
SELF
SEMI
SENSITIVE
SEPARATE
SEQUENCE
SERIALIZABLE
SESSION
SET
SETS
SHARD
SHARE
SHARED
SHORT
SHOW
SIGNAL
SIMILAR
SIZE
SKEWED
SMALLINT
SNAPSHOT
SOME
SOURCE
SPACE
SPACES
SPARSE
SPECIFIC
SPECIFICTYPE
SPLIT
SQL
SQLCODE
SQLERROR
SQLEXCEPTION
SQLSTATE
SQLWARNING
START
STATE
STATIC
STATUS
STORAGE
STORE
STORED
STREAM
STRING
STRUCT
STYLE
SUB
SUBMULTISET
SUBPARTITION
SUBSTRING
SUBTYPE
SUM
SUPER
SYMMETRIC
SYNONYM
SYSTEM
TABLE
TABLESAMPLE
TEMP
TEMPORARY
TERMINATED
TEXT
THAN
THEN
THROUGHPUT
TIME
TIMESTAMP
TIMEZONE
TINYINT
TO
TOKEN
TOTAL
TOUCH
TRAILING
TRANSACTION
TRANSFORM
TRANSLATE
TRANSLATION
TREAT
TRIGGER
TRIM
TRUE
TRUNCATE
TTL
TUPLE
TYPE
UNDER
UNDO
UNION
UNIQUE
UNIT
UNKNOWN
UNLOGGED
UNNEST
UNPROCESSED
UNSIGNED
UNTIL
UPDATE
UPPER
URL
USAGE
USE
USER
USERS
USING
UUID
VACUUM
VALUE
VALUED
VALUES
VARCHAR
VARIABLE
VARIANCE
VARINT
VARYING
VIEW
VIEWS
VIRTUAL
VOID
WAIT
WHEN
WHENEVER
WHERE
WHILE
WINDOW
WITH
WITHIN
WITHOUT
WORK
WRAPPED
WRITE
YEAR
ZONE 
```








