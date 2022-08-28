
## Tradeoffs

* Understand the areas where performance is most critical: Understand and identify areas where increasing the performance
  of your workload will have a positive impact on efficiency or customer experience. For example, a website that has a 
  large amount of customer interaction can benefit from using edge services to move content delivery closer to customers.
* Learn about design patterns and services: Research and understand the various design patterns and services that help 
  improve workload performance. As part of the analysis, identify what you could trade to achieve higher performance. 
  For example, using a cache service can help to reduce the load placed on database systems; however, it requires some
  engineering to implement safe caching or possible introduction of eventual consistency in some areas.
* Identify how tradeoffs impact customers and efficiency: When evaluating performance-related improvements, determine 
  which choices will impact your customers and workload efficiency. For example, if using a key-value data store 
  increases system performance, it is important to evaluate how the eventually consistent nature of it will impact customers.
* Measure the impact of performance improvements: As changes are made to improve performance, evaluate the collected 
  metrics and data. Use this information to determine impact that the performance improvement had on the workload, the workload’s components, and your customers. This measurement helps you understand the improvements that result from the tradeoff, and helps you determine if any negative side-effects were introduced.
* Use various performance-related strategies: Where applicable, utilize multiple strategies to improve performance.
  For example, using strategies like caching data to prevent excessive network or database calls, using read-replicas for database engines to improve read rates, sharding or compressing data where possible to reduce data volumes, and buffering and streaming of results as they are available to avoid blocking.

## Asynchronous vs Synchronous 

Synchronous invocations are well suited for short-lived Lambda functions. Although Lambda functions can run for up to
15 minutes, synchronous callers may have shorter timeouts. For example, API Gateway has a 29-second integration timeout,
so a Lambda function running for more than 29 seconds will not return a value successfully. In synchronous invocations,
if the Lambda function fails, retries are the responsibility of the trigger.

In asynchronous invocations, the caller continues with other work and cannot receive a return value from the Lambda
function. The function can send the result to a destination, configurable based on success or failure. The internal queue
between the caller and the function ensures that messages are stored durably. The Lambda service scales up the 
concurrency of the processing function as this internal queue grows. Lambda handles retries if the function returns an 
error or is throttled. 
To customize this behavior, you can configure error handling settings on a function, version, or alias. 
You can also configure Lambda to send events that failed processing to a dead-letter queue, or to send a 
record of any invocation to a destination.

The specific retry behavior for processing SQS messages is determined in the SQS queue configuration. Here you can 
set the visibility timeout, message retention period, and delivery delay.

If a Lambda function throws an error, the Lambda service continues to process the failed message until:

* The message is processed without any error from the function, and the service deletes the message from the queue.
* The Message retention period is reached and SQS deletes the message from the queue.
* There is a dead-letter queue (DLQ) configured and SQS sends the message to this queue. It’s best practice to enable a 
  DLQ on an SQS queue to prevent any message loss.

Lambda does not delete messages from the queue unless there is a successful invocation. If any messages in a batch fail,
all messages are returned to the original queue for reprocessing. In an application under heavy load or with spiky 
traffic patterns, it’s recommended that you:

* Set the queue’s visibility timeout to at least six times the function timeout value. This allows the function time to
  process each batch of records if the function execution is throttled while processing a previous batch.
* Set the maxReceiveCount on the source queue’s redrive policy to at least 5. This improves the chances of messages being
  processed before reaching the DLQ.
* Ensure idempotency to allow messages to be safely processed more than once.

**Note** that an SQS DLQ is different to a Lambda DLQ, which is used for the function’s asynchronous invocation queue,
not for event source queues.

## Kinesis Streams

A Kinesis data stream is a set of shards. Each shard has a sequence of data records. Each data record has a sequence number
that is assigned by Kinesis Data Streams. A stream is composed of one or more shards, each of which provides a 
fixed unit of capacity. Each shard can support up to 5 transactions per second for reads, up to a 
maximum total data read rate of 2 MB per second and up to 1,000 records per second for writes, up to a 
maximum total data write rate of 1 MB per second (including partition keys). The data capacity of your 
stream is a function of the number of shards that you specify for the stream. The total capacity of the stream 
is the sum of the capacities of its shards.

A partition key is used to group data by shard within a stream. Kinesis Data Streams segregates the data records
belonging to a stream into multiple shards. It uses the partition key that is associated with each data record to
determine which shard a given data record belongs to. Partition keys are Unicode strings, with a maximum length 
limit of 256 characters for each key. An MD5 hash function is used to map partition keys to 128-bit integer values 
and to map associated data records to shards using the hash key ranges of the shards. When an application puts
data into a stream, it must specify a partition key. 

Each data record has a sequence number that is unique per partition-key within its shard. Kinesis Data Streams 
assigns the sequence number after you write to the stream with client.putRecords or client.putRecord. 
Sequence numbers for the same partition key generally increase over time. The longer the time period 
between write requests, the larger the sequence numbers become.

The retention period is the length of time that data records are accessible after they are added to the stream. 
A stream’s retention period is set to a default of 24 hours after creation. You can increase the retention 
period up to 8760 hours (365 days)  and decrease the retention period down to a minimum of 24 hours.


### Consumers

One of the methods of developing custom consumer applications that can process data from KDS data streams is to use the 
Kinesis Client Library (KCL).

KCL helps you consume and process data from a Kinesis data stream by taking care of many of the complex tasks associated
with distributed computing. These include load balancing across multiple consumer application instances, responding to 
consumer application instance failures, checkpointing processed records, and reacting to resharding. The KCL takes 
care of all of these subtasks so that you can focus your efforts on writing your custom record-processing logic.

Propagation delay is defined as the end-to-end latency from the moment a record is written to the stream until it is
read by a consumer application. This delay varies depending upon a number of factors, but it is primarily affected by the
polling interval of consumer applications.

For most applications, we recommend polling each shard one time per second per application. This enables you to have
multiple consumer applications processing a stream concurrently without hitting Amazon Kinesis Data Streams limits of 
5 GetRecords calls per second. Additionally, processing larger batches of data tends to be more efficient at reducing
network and other downstream latencies in your application.

The KCL defaults are set to follow the best practice of polling every 1 second. This default results in average 
propagation delays that are typically below 1 second.

Kinesis Data Streams records are available to be read immediately after they are written. There are some use 
cases that need to take advantage of this and require consuming data from the stream as soon as it is available.
You can significantly reduce the propagation delay by overriding the KCL default settings to poll more 
frequently, as shown in the following examples.

The following example illustrates how the KCL helps you handle scaling and resharding:

* For example, if your application is running on one EC2 instance, and is processing one Kinesis data stream that has 
  four shards. This one instance has one KCL worker and four record processors (one record processor for every shard). 
  These four record processors run in parallel within the same process.
* Next, if you scale the application to use another instance, you have two instances processing one stream that has 
  four shards. When the KCL worker starts up on the second instance, it load-balances with the first instance, 
  so that each instance now processes two shards.
* If you then decide to split the four shards into five shards. The KCL again coordinates the processing across 
  instances: one instance processes three shards, and the other processes two shards. A similar coordination 
  occurs when you merge shards.

Typically, when you use the KCL, you should ensure that the number of instances does not exceed the number of shards 
(except for failure standby purposes). Each shard is processed by exactly one KCL worker and has exactly one corresponding 
record processor, so you never need multiple instances to process one shard. However, one worker can process any number 
of shards, so it's fine if the number of shards exceeds the number of instances.

To scale up processing in your application, you should test a combination of these approaches:

* Increasing the instance size (because all record processors run in parallel within a process)
* Increasing the number of instances up to the maximum number of open shards (because shards can be processed independently)
* Increasing the number of shards (which increases the level of parallelism)

Note that you can use Auto Scaling to automatically scale your instances based on appropriate metrics.

When resharding increases the number of shards in the stream, the corresponding increase in the number of record 
processors increases the load on the EC2 instances that are hosting them. If the instances are part of an Auto Scaling 
group, and the load increases sufficiently, the Auto Scaling group adds more instances to handle the increased load. 
You should configure your instances to launch your Amazon Kinesis Data Streams application at startup, 
so that additional workers and record processors become active on the new instance right away.

You can also use a AWS Lambda function to process records in a data stream. AWS Lambda is a compute service that lets you 
run code without provisioning or managing servers. It executes your code only when needed and scales automatically, 
from a few requests per day to thousands per second. You pay only for the compute time you consume. There is no charge 
when your code is not running. With AWS Lambda, you can run code for virtually any type of application or backend 
service, all with zero administration. It runs your code on a high-availability compute infrastructure and 
performs all of the administration of the compute resources, including server and operating system maintenance, 
capacity provisioning and automatic scaling, code monitoring and logging.

You can use a Kinesis Data Firehose to read and process records from a Kinesis stream. Kinesis Data Firehose is a 
fully managed service for delivering real-time streaming data to destinations such as Amazon S3, Amazon Redshift, 
Amazon OpenSearch Service, and Splunk. Kinesis Data Firehose also supports any custom HTTP endpoint or HTTP endpoints
owned by supported third-party service providers, including Datadog, MongoDB, and New Relic. You can also configure 
Kinesis Data Firehose to transform your data records and to convert the record format before delivering your data 
to its destination. 


## Application to Application  messaging 


Amazon SNS and AWS Lambda are integrated so you can invoke Lambda functions with Amazon SNS notifications. 
When a message is published to an SNS topic that has a Lambda function subscribed to it, the Lambda function is 
invoked with the payload of the published message. The Lambda function receives the message payload as an input 
parameter and can manipulate the information in the message, publish the message to other SNS topics, or send the 
message to other AWS services.

Amazon SNS works closely with Amazon Simple Queue Service (Amazon SQS). These services provide different benefits for 
developers. Amazon SNS allows applications to send time-critical messages to multiple subscribers through a “push” 
mechanism, eliminating the need to periodically check or “poll” for updates. Amazon SQS is a message queue service 
used by distributed applications to exchange messages through a polling model, and can be used to decouple 
sending and receiving components—without requiring each component to be concurrently available. Using Amazon SNS and 
Amazon SQS together, messages can be delivered to applications that require immediate notification of an event, and 
also persisted in an Amazon SQS queue for other applications to process at a later time.

When you subscribe an Amazon SQS queue to an Amazon SNS topic, you can publish a message to the topic and Amazon SNS 
sends an Amazon SQS message to the subscribed queue. The Amazon SQS message contains the subject and message that were
published to the topic along with metadata about the message in a JSON document. You can subscribe multiple SQS queues to
SNS topic (this is called fanout)

## Polling messages 

Amazon SQS provides short polling and long polling to receive messages from a queue. 
By default, queues use short polling.

When you consume messages from a queue using short polling, Amazon SQS samples a subset of its servers (based on a 
weighted random distribution) and returns messages from only those servers. Thus, a particular ReceiveMessage request
might not return all of your messages. However, if you have fewer than 1,000 messages in your queue, a subsequent
request will return your messages. If you keep consuming from your queues, Amazon SQS samples all of its servers,
and you receive all of your messages.
When the wait time for the ReceiveMessage API action is greater than 0, long polling is in effect. 
The maximum long polling wait time is 20 seconds. Long polling helps reduce the cost of using Amazon SQS by 
eliminating the number of empty responses (when there are no messages available for a ReceiveMessage request)
and false empty responses (when messages are available but aren't included in a response). 

Long polling offers the following benefits:
* Reduce empty responses by allowing Amazon SQS to wait until a message is available in a queue before sending a response.
  Unless the connection times out, the response to the ReceiveMessage request contains at least one of the available messages,
  up to the maximum number of messages specified in the ReceiveMessage action. In rare cases, you might receive empty 
  responses even when a queue still contains messages, especially if you specify a low value for the 
  ReceiveMessageWaitTimeSeconds parameter.
* Reduce false empty responses by querying all—rather than a subset of—Amazon SQS servers.
* Return messages as soon as they become available.

The visibility timeout begins when Amazon SQS returns a message. During this time, the consumer processes and deletes 
the message. However, if the consumer fails before deleting the message and your system doesn't call the DeleteMessage 
action for that message before the visibility timeout expires, the message becomes visible to other consumers and 
the message is received again. If a message must be received only once, your consumer should delete it within 
the duration of the visibility timeout.
Every Amazon SQS queue has the default visibility timeout setting of 30 seconds. You can change this setting 
for the entire queue. Typically, you should set the visibility timeout to the maximum time that it takes your 
application to process and delete a message from the queue. When receiving messages, you can also set a 
special visibility timeout for the returned messages without changing the overall queue timeout.
If you don't know how long it takes to process a message, create a heartbeat for your consumer process: Specify the 
initial visibility timeout (for example, 2 minutes) and then—as long as your consumer still works on the message—keep
extending the visibility timeout by 2 minutes every minute.

Delay queues let you postpone the delivery of new messages to consumers for a number of seconds, for example, 
when your consumer application needs additional time to process messages. If you create a delay queue,
any messages that you send to the queue remain invisible to consumers for the duration of the delay period. 
The default (minimum) delay for a queue is 0 seconds. The maximum is 15 minutes.
Delay queues are similar to visibility timeouts because both features make messages unavailable to consumers for 
a specific period of time. The difference between the two is that, for delay queues, a message is hidden when it 
is first added to queue, whereas for visibility timeouts a message is hidden only after it is consumed from the queue.
The following diagram illustrates the relationship between delay queues and visibility timeouts.

