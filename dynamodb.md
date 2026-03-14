1. Introduction to DynamoDB

Amazon DynamoDB is a fully managed NoSQL database service provided by AWS that delivers fast and predictable performance with seamless scalability.

Key characteristics:

Fully managed database

Serverless architecture

Single-digit millisecond latency

Automatic scaling

Built-in security and backup

High availability across multiple Availability Zones

Common use cases include:

Gaming applications

IoT applications

Real-time analytics

E-commerce platforms

Serverless architectures

2. Core Concepts
Tables

A DynamoDB table stores data.

Example:

Table: Users

Tables contain items.

Items

Items are equivalent to rows in relational databases.

Example:

{
  "UserID": "123",
  "Name": "Shivangi",
  "Age": 30
}

Each item is uniquely identified by a primary key.

Maximum item size:

400 KB
Attributes

Attributes are fields in an item.

Example:

UserID
Name
Age

DynamoDB supports flexible schema:

Different items can have different attributes.

3. Primary Keys

Every DynamoDB table requires a primary key.

There are two types.

Partition Key

Single attribute primary key.

Example:

UserID

DynamoDB distributes items across partitions based on this key.

Composite Primary Key

Consists of:

Partition Key
Sort Key

Example:

Partition Key: UserID
Sort Key: OrderID

Example item:

UserID = 123
OrderID = 456

This allows multiple items with the same partition key.

4. Data Types

DynamoDB supports multiple attribute types.

Scalar Types

String

S

Number

N

Binary

B

Boolean

BOOL

Null

NULL
Document Types

Map

Example:

{
  "address": {
      "city": "Delhi",
      "zip": "110001"
  }
}

List

Example:

["apple","banana","orange"]
Set Types

String Set

SS

Number Set

NS

Binary Set

BS
5. Read and Write Operations
PutItem

Creates or replaces an item.

Example:

PutItem
{
  "UserID": "123",
  "Name": "Shivangi"
}
GetItem

Retrieves an item using primary key.

Example:

GetItem
UserID = 123
UpdateItem

Modifies attributes of an item.

Example:

SET Age = 31
DeleteItem

Deletes an item.

Example:

DeleteItem
UserID = 123
6. Query vs Scan
Query

Retrieves items based on partition key.

Example:

Query
PartitionKey = 123

Query is efficient.

Scan

Reads every item in the table.

Example:

Scan table

Scan is expensive and slow for large tables.

7. Secondary Indexes

Indexes allow alternative query patterns.

Two types exist.

Global Secondary Index (GSI)

Allows querying using a different partition key.

Example:

Table PK = UserID
GSI PK = Email
Local Secondary Index (LSI)

Uses same partition key but different sort key.

Example:

PK = UserID
SK = OrderDate
8. Capacity Modes

DynamoDB provides two capacity modes.

On-Demand Mode

Automatically scales capacity.

You pay per request.

Best for unpredictable workloads.

Provisioned Mode

You define capacity units.

Read Capacity Units (RCU)
Write Capacity Units (WCU)

More cost efficient for predictable workloads.

9. Consistency Models
Eventually Consistent Reads

Default behavior.

Data may take time to propagate.

Lower cost.

Strongly Consistent Reads

Returns the most recent data.

Higher latency and cost.

10. Transactions

DynamoDB supports ACID transactions.

Operations include:

TransactWriteItems
TransactGetItems

These allow multiple operations to succeed or fail together.

Example use case:

Deduct money

Add purchased item

Both must succeed.

11. Streams

DynamoDB Streams capture table changes.

Events include:

INSERT
MODIFY
REMOVE

Common use case:

Trigger AWS Lambda functions.

Example flow:

DynamoDB → Stream → Lambda
12. Backup and Restore

DynamoDB provides:

On-demand backups

Manual backup

Point-in-time recovery

Restore to any second within last 35 days
13. Security

Security features include:

IAM access control

Example:

Allow read access
Allow write access

Encryption at rest

Encryption in transit

VPC endpoints

14. Limits

Important DynamoDB limits.

Item size

400 KB

Batch write limit

25 items

Batch read limit

100 items

Transaction limit

100 operations
15. Data Modeling Best Practices

Important design principles.

Design for access patterns.

Avoid joins.

Use denormalized data.

Keep items small.

Use composite keys.

Example key structure:

PK = USER#123
SK = ORDER#456
16. Common Design Patterns
Single Table Design

Store multiple entities in one table.

Example:

PK         SK
USER#1     PROFILE
USER#1     ORDER#100
USER#1     ORDER#101
Adjacency List Pattern

Used for graph relationships.

Example:

PK = USER#123
SK = FRIEND#456
Time Series Pattern

Example:

PK = DEVICE#123
SK = TIMESTAMP
17. Migration from Relational Databases

Migration approaches:

Offline migration

Application downtime allowed.

Hybrid migration

Read-only mode allowed.

Online migration

No downtime required.

Tools used:

AWS DMS
AWS Glue
Amazon EMR
Custom ETL scripts



## standard vs standard IA class
DynamoDB Standard: Best when throughput (reads/writes) is the main cost. It has lower throughput costs.
DynamoDB Standard-IA: Best when storage is the main cost. It has lower storage costs.
If storage cost becomes more than ~50% of throughput cost, switching to Standard-IA can reduce total cost.
Both table classes provide the same performance, durability, and availability.

# Global tables
-consistency modes cannot be changed after creation. multi-Region eventual consistency (MREC)(default) and multi-Region strong consistency (MRSC)
-MRSC does not support TTL, transactions
| Feature      | MREC      | MRSC          |
| ------------ | --------- | ------------- |
| Consistency  | Eventual  | Strong        |
| Replication  | Async     | Sync          |
| Streams      | Used      | Not used      |
| Transactions | Supported | Not supported |
| TTL          | Supported | Not supported |


