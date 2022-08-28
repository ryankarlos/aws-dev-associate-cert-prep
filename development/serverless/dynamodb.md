In DynamoDB, tables, items, and attributes are the core components that you work with. A table is a 
collection of items, and each item is a collection of attributes. DynamoDB uses primary keys to uniquely 
identify each item in a table and secondary indexes to provide more querying flexibility. You can use 
DynamoDB Streams to capture data modification events in DynamoDB tables.

The following are the basic DynamoDB components:

* Tables – Similar to other database systems, DynamoDB stores data in tables. A table is a collection of data. 
  For example, see the example table called People that you could use to store personal contact information about 
  friends, family, or anyone else of interest. You could also have a Cars table to store information about vehicles 
  that people drive.

* Items – Each table contains zero or more items. An item is a group of attributes that is uniquely identifiable 
  among all of the other items. In a People table, each item represents a person. For a Cars table, each item 
  represents one vehicle. Items in DynamoDB are similar in many ways to rows, records, or tuples in other database 
  systems. In DynamoDB, there is no limit to the number of items you can store in a table.

* Attributes – Each item is composed of one or more attributes. An attribute is a fundamental data element, something 
  that does not need to be broken down any further. For example, an item in a People table contains attributes 
  called PersonID, LastName, FirstName, and so on. For a Department table, an item might have attributes such as 
  DepartmentID, Name, Manager, and so on. Attributes in DynamoDB are similar in many ways to fields or columns in other 
  database systems.
  

## Primary key

When you create a table, in addition to the table name, you must specify the primary key of the table. 
The primary key uniquely identifies each item in the table, so that no two items can have the same key.
Each primary key attribute must be a scalar (meaning that it can hold only a single value). 
The only data types allowed for primary key attributes are string, number, or binary. There are no such 
restrictions for other, non-key attributes.

DynamoDB supports two different kinds of primary keys:

* Partition key – A simple primary key, composed of one attribute known as the partition key. 
  DynamoDB uses the partition key's value as input to an internal hash function. 
  The output from the hash function determines the partition (physical storage internal to DynamoDB) 
  in which the item will be stored.
  In a table that has only a partition key, no two items can have the same partition key value.
  The partition key of an item is also known as its hash attribute. The term hash attribute derives from the use of an 
internal hash function in DynamoDB that evenly distributes data items across partitions, based on their partition key values. 
  
* Partition key and sort key – Referred to as a composite primary key, this type of key is composed of two attributes. 
  The first attribute is the partition key, and the second attribute is the sort key. 
DynamoDB uses the partition key value as input to an internal hash function. The output from the hash function 
  determines the partition (physical storage internal to DynamoDB) in which the item will be stored. All items 
  with the same partition key value are stored together, in sorted order by sort key value. 
  In a table that has a partition key and a sort key, it's possible for multiple items to have the same partition key 
  value. However, those items must have different sort key values. 
  The sort key of an item is also known as its range attribute. The term range attribute derives from the way DynamoDB 
  stores items with the same partition key physically close together, in sorted order by the sort key value.

## Secondary Index

You can create one or more secondary indexes on a table. A secondary index lets you query the data in the table 
using an alternate key, in addition to queries against the primary key. DynamoDB doesn't require that you use 
indexes, but they give your applications more flexibility when querying your data. After you create a secondary
index on a table, you can read data from the index in much the same way as you do from the table.

DynamoDB supports two kinds of indexes:

Global secondary index – An index with a partition key and sort key that can be different from those on the table.
Local secondary index – An index that has the same partition key as the table, but a different sort key.

Each table in DynamoDB has a quota of 20 global secondary indexes (default quota) and 5 local secondary indexes.

## Data Types

DynamoDB supports many different data types for attributes within a table. They can be categorized as follows:

* Scalar Types – A scalar type can represent exactly one value. The scalar types are number, string, binary, Boolean, and null.
* Document Types – A document type can represent a complex structure with nested attributes, such as you would find in a JSON document. The document types are list and map.
* Set Types – A set type can represent multiple scalar values. The set types are string set, number set, and binary set.

When you create a table or a secondary index, you must specify the names and data types of each primary key attribute (partition key and sort key). 
Furthermore, each primary key attribute must be defined as type string, number, or binary.
DynamoDB is a NoSQL database and is schemaless. This means that, other than the primary key attributes, you don't have to 
define any attributes or data types when you create tables. By comparison, relational databases require you to define 
the names and data types of each column when you create a table.


## DynamoDB Streams

DynamoDB Streams is an optional feature that captures data modification events in DynamoDB tables. The data about 
these events appear in the stream in near-real time, and in the order that the events occurred.

Each event is represented by a stream record. If you enable a stream on a table, DynamoDB Streams writes a 
stream record whenever one of the following events occurs:

* A new item is added to the table: The stream captures an image of the entire item, including all of its attributes.
* An item is updated: The stream captures the "before" and "after" image of any attributes that were modified in the item.
* An item is deleted from the table: The stream captures an image of the entire item before it was deleted.

Each stream record also contains the name of the table, the event timestamp, and other metadata. Stream records have a 
lifetime of 24 hours; after that, they are automatically removed from the stream.
You can use DynamoDB Streams together with AWS Lambda to create a trigger—code that runs automatically whenever an 
event of interest appears in a stream. For example, consider a Customers table that contains customer information for 
a company. Suppose that you want to send a "welcome" email to each new customer. You could enable a stream on that 
table, and then associate the stream with a Lambda function. The Lambda function would run whenever a new stream record 
appears, but only process new items added to the Customers table. For any item that has an EmailAddress attribute, the 
Lambda function would invoke Amazon Simple Email Service (Amazon SES) to send an email to that address.

### API
* ListStreams – Returns a list of all your streams, or just the stream for a specific table.
* DescribeStream – Returns information about a stream, such as its Amazon Resource Name (ARN) and where your
  application can begin reading the first few stream records.
* GetShardIterator – Returns a shard iterator, which is a data structure that your application uses to retrieve 
  the records from the stream.
* GetRecords – Retrieves one or more stream records, using a given shard iterator.

## Performing CRUD or Data Plane operations

You can use PartiQL - a SQL-compatible query language for Amazon DynamoDB, to perform these CRUD operations or you can use 
DynamoDB’s classic CRUD APIs that separates each operation into a distinct API call.


### PartiQL

ExecuteStatement – Reads multiple items from a table. You can also write or update a single item from a table.
When writing or updating a single item, you must specify the primary key attributes.
BatchExecuteStatement – Writes, updates or reads multiple items from a table. This is more efficient than 
ExecuteStatement because your application only needs a single network round trip to write or read the items.


### Classic Crud API

**Creating data**

* PutItem – Writes a single item to a table. You must specify the primary key attributes, but you don't have to 
  specify other attributes.
* BatchWriteItem – Writes up to 25 items to a table. This is more efficient than calling PutItem multiple times 
  because your application only needs a single network round trip to write the items. You can also use BatchWriteItem 
  for deleting multiple items from one or more tables.

**Reading data**

* GetItem – Retrieves a single item from a table. You must specify the primary key for the item that you want. You can 
  retrieve the entire item, or just a subset of its attributes.

* BatchGetItem – Retrieves up to 100 items from one or more tables. This is more efficient than calling GetItem multiple
  times because your application only needs a single network round trip to read the items.

* Query – Retrieves all items that have a specific partition key. You must specify the partition key value. You can 
  retrieve entire items, or just a subset of their attributes. Optionally, you can apply a condition to the sort key 
  values so that you only retrieve a subset of the data that has the same partition key. You can use this operation 
  on a table, provided that the table has both a partition key and a sort key. You can also use this operation on an 
  index, provided that the index has both a partition key and a sort key.

* Scan – Retrieves all items in the specified table or index. You can retrieve entire items, or just a subset of their 
  attributes. Optionally, you can apply a filtering condition to return only the values that you are interested in and 
  discard the rest.


## Read Consistency 

Amazon DynamoDB is available in multiple AWS Regions around the world. Each Region is independent and isolated from 
other AWS Regions. For example, if you have a table called People in the us-east-2 Region and another table 
named People in the us-west-2 Region, these are considered two entirely separate tables. For a list of 
all the AWS Regions in which DynamoDB is available, see AWS Regions and Endpoints in the Amazon Web Services General Reference.

Every AWS Region consists of multiple distinct locations called Availability Zones. Each Availability Zone is isolated 
from failures in other Availability Zones, and provides inexpensive, low-latency network connectivity to other 
Availability Zones in the same Region. This allows rapid replication of your data among multiple Availability 
Zones in a Region.

When your application writes data to a DynamoDB table and receives an HTTP 200 response (OK), the write has occurred 
and is durable. The data is eventually consistent across all storage locations, usually within one second or less.

DynamoDB supports eventually consistent and strongly consistent reads.

**Eventually Consistent Reads**

When you read data from a DynamoDB table, the response might not reflect the results of a recently completed write 
operation. The response might include some stale data. If you repeat your read request after a short time, 
the response should return the latest data.

**Strongly Consistent Reads**

When you request a strongly consistent read, DynamoDB returns a response with the most up-to-date data, reflecting 
the updates from all prior write operations that were successful. However, this consistency comes with 
some disadvantages:

* A strongly consistent read might not be available if there is a network delay or outage. In this case,
* DynamoDB may return a server error (HTTP 500). 
* Strongly consistent reads may have higher latency than eventually consistent reads.
* Strongly consistent reads are not supported on global secondary indexes.
* Strongly consistent reads use more throughput capacity than eventually consistent reads.

DynamoDB uses eventually consistent reads, unless you specify otherwise. 
Read operations (such as GetItem, Query, and Scan) provide a ConsistentRead parameter. 
If you set this parameter to true, DynamoDB uses strongly consistent reads during the operation.

## Read/write capacity mode

Amazon DynamoDB has two read/write capacity modes for processing reads and writes on your tables: On-demand
or  Provisioned (default, free-tier eligible).
The read/write capacity mode controls how you are charged for read and write throughput and how you manage capacity. 
You can set the read/write capacity mode when creating a table or you can change it later.


### On Demand

Amazon DynamoDB on-demand is a flexible billing option capable of serving thousands of requests per 
second without capacity planning. DynamoDB on-demand offers pay-per-request pricing for read and write 
requests so that you pay only for what you use.

When you choose on-demand mode, DynamoDB instantly accommodates your workloads as they ramp up or down to any 
previously reached traffic level. If a workload’s traffic level hits a new peak, DynamoDB adapts rapidly to 
accommodate the workload. Tables that use on-demand mode deliver the same single-digit millisecond 
latency, service-level agreement (SLA) commitment, and security that DynamoDB already offers. You can 
choose on-demand for both new and existing tables and you can continue using the existing DynamoDB APIs without 
changing code.

On-demand mode is a good option if any of the following are true:

* You create new tables with unknown workloads.
* You have unpredictable application traffic.
* You prefer the ease of paying for only what you use.

### Provisioned 

If you choose provisioned mode, you specify the number of reads and writes per second that you 
require for your application. You can use auto scaling to adjust your table’s provisioned capacity automatically 
in response to traffic changes. This helps you govern your DynamoDB use to stay at or below a defined 
request rate in order to obtain cost predictability.

Provisioned mode is a good option if any of the following are true:

* You have predictable application traffic.
* You run applications whose traffic is consistent or ramps gradually.
* You can forecast capacity requirements to control costs.

### RCU and WCU 

For on-demand mode tables, you don't need to specify how much read and write throughput you expect your application to perform.
For provisioned mode tables, you specify throughput capacity in terms of read capacity units (RCUs) and write capacity 
units (WCUs).  DynamoDB charges you for the reads and writes that your application performs on your tables in terms of read 
request units and write request units. 

DynamoDB read requests can be either strongly consistent, eventually consistent, or transactional.

* A strongly consistent read request of up to 4 KB requires one read request unit.
* An eventually consistent read request of up to 4 KB requires one-half read request unit.
* A transactional read request of up to 4 KB requires two read request units.

If you need to read an item that is larger than 4 KB, DynamoDB needs additional read request units. The total number 
of read request units required depends on the item size, and whether you want an eventually consistent or strongly consistent read. 
For example, if your item size is 8 KB, you require 2 read request units to sustain one strongly consistent 
read, 1 read request unit if you choose eventually consistent reads, or 4 read request units for a transactional read request.

One write capacity unit represents one write per second for an item up to 1 KB in size. If you need to write an item 
that is larger than 1 KB, DynamoDB must consume additional write capacity units. Transactional write requests 
require 2 write capacity units to perform one write per second for items up to 1 KB. The total number of write capacity units required depends on the item size. For example, if your item size is 2 KB, you require 2 write capacity units to sustain one write request per second or 4 write capacity units for a transactional write request. 
If your application reads or writes larger items (up to the DynamoDB maximum item size of 400 KB), it will consume more capacity units.

For example, suppose that you create a provisioned table with 6 read capacity units and 
6 write capacity units. With these settings, your application could do the following:

* Perform strongly consistent reads of up to 24 KB per second (4 KB × 6 read capacity units).
* Perform eventually consistent reads of up to 48 KB per second (twice as much read throughput).
* Perform transactional read requests of up to 12 KB per second.
* Write up to 6 KB per second (1 KB × 6 write capacity units).
* Perform transactional write requests of up to 3 KB per second.

Provisioned throughput is the maximum amount of capacity that an application can consume from a table or index. 
If your application exceeds your provisioned throughput capacity on a table or index, it is subject to request throttling.

Throttling prevents your application from consuming too many capacity units. When a request is throttled, it fails with an 
HTTP 400 code (Bad Request) and a ProvisionedThroughputExceededException. The AWS SDKs have built-in support for retrying 
throttled requests (see Error retries and exponential backoff), so you do not need to write this logic yourself.

## AutoScaling


DynamoDB auto scaling actively manages throughput capacity for tables and global secondary indexes. With auto scaling, 
you define a range (upper and lower limits) for read and write capacity units. You also define a target utilization 
percentage within that range. DynamoDB auto scaling seeks to maintain your target utilization, even as your 
application workload increases or decreases.
With DynamoDB auto scaling, a table or a global secondary index can increase its provisioned read and write 
capacity to handle sudden increases in traffic, without request throttling. When the workload decreases, DynamoDB auto
scaling can decrease the throughput so that you don't pay for unused provisioned capacity.

## Reserved capacity 

As a DynamoDB customer, you can purchase reserved capacity in advance for tables that use the DynamoDB Standard table 
class, as described at Amazon DynamoDB Pricing. With reserved capacity, you pay a one-time upfront fee and commit 
to a minimum provisioned usage level over a period of time. Your reserved capacity is billed at the hourly reserved 
capacity rate. By reserving your read and write capacity units ahead of time, you realize significant cost savings 
on your provisioned capacity costs. Any capacity that you provision in excess of your reserved capacity is billed 
at standard provisioned capacity rates.

## DAX

Amazon DynamoDB is designed for scale and performance. In most cases, the DynamoDB response times can be measured in single-digit milliseconds. 
However, there are certain use cases that require response times in microseconds. For these use cases, DynamoDB Accelerator (DAX) 
delivers fast response times for accessing eventually consistent data.
DAX is a DynamoDB-compatible caching service that enables you to benefit from fast in-memory performance for demanding 
applications. DAX addresses three core scenarios:

* As an in-memory cache, DAX reduces the response times of eventually consistent read workloads by an order of magnitude 
  from single-digit milliseconds to microseconds.
* DAX reduces operational and application complexity by providing a managed service that is API-compatible with DynamoDB. 
  Therefore, it requires only minimal functional changes to use with an existing application.
* For read-heavy or bursty workloads, DAX provides increased throughput and potential operational cost savings by reducing 
  the need to overprovision read capacity units. This is especially beneficial for applications that require repeated reads for individual keys.

DAX supports server-side encryption. With encryption at rest, the data persisted by DAX on disk will be encrypted. DAX 
writes data to disk as part of propagating changes from the primary node to read replicas. For more information, see DAX encryption at rest.

DAX also supports encryption in transit, ensuring that all requests and responses between your application and the cluster 
are encrypted by transport level security (TLS), and connections to the cluster can be authenticated by verification of a cluster x509 certificate.