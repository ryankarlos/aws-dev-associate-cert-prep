## Improving S3 read performance

Your applications can easily achieve thousands of transactions per second in request performance when uploading and retrieving storage from Amazon S3. 
Amazon S3 automatically scales to high request rates. For example, your application can achieve at least 3,500 PUT/COPY/POST/DELETE or 5,500 GET/HEAD 
requests per second per partitioned prefix. There are no limits to the number of prefixes in a bucket. You can increase your read or write 
performance by using parallelization. For example, if you create 10 prefixes in an Amazon S3 bucket to parallelize reads, 
you could scale your read performance to 55,000 read requests per second. Similarly, you can scale write operations by writing 
to multiple prefixes. For more information about creating and using prefixes, see Organizing objects using prefixes.

Some data lake applications on Amazon S3 scan millions or billions of objects for queries that run over petabytes of data. 
These data lake applications achieve single-instance transfer rates that maximize the network interface use for their Amazon 
EC2 instance, which can be up to 100 Gb/s on a single instance. These applications then aggregate throughput across multiple 
instances to get multiple terabits per second.

Other applications are sensitive to latency, such as social media messaging applications. These applications can achieve 
consistent small object latencies (and first-byte-out latencies for larger objects) of roughly 100–200 milliseconds.

Other AWS services can also help accelerate performance for different application architectures. For example, if you want 
higher transfer rates over a single HTTP connection or single-digit millisecond latencies, use Amazon CloudFront or Amazon 
ElastiCache for caching with Amazon S3.

Additionally, if you want fast data transport over long distances between a client and an S3 bucket, use Configuring fast, 
secure file transfers using Amazon S3 Transfer Acceleration. Transfer Acceleration uses the globally distributed edge 
locations in CloudFront to accelerate data transport over geographical distances. Transfer Acceleration is ideal for 
transferring gigabytes to terabytes of data regularly across continents. It's also useful for clients that upload to a centralized bucket from all over the world.


## Frequently accessed s3 content

Many applications that store data in Amazon S3 serve a “working set” of data that is repeatedly requested by users. If 
a workload is sending repeated GET requests for a common set of objects, you can use a cache such as Amazon CloudFront, 
Amazon ElastiCache, or AWS Elemental MediaStore to optimize performance. Successful cache adoption can result in low 
latency and high data transfer rates. Applications that use caching also send fewer direct requests to Amazon S3, which 
can help reduce request costs.

Amazon CloudFront is a fast content delivery network (CDN) that transparently caches data from Amazon S3 in a large set 
of geographically distributed points of presence (PoPs). When objects might be accessed from multiple Regions, or over the internet, 
CloudFront allows data to be cached close to the users that are accessing the objects. This can result in high performance 
delivery of popular Amazon S3 content

Amazon ElastiCache is a managed, in-memory cache. With ElastiCache, you can provision Amazon EC2 instances that cache objects 
in memory. This caching results in orders of magnitude reduction in GET latency and substantial increases in download throughput. 
To use ElastiCache, you modify application logic to both populate the cache with hot objects and check the cache for hot 
objects before requesting them from Amazon S3.

AWS Elemental MediaStore is a caching and content distribution system specifically built for video workflows and media 
delivery from Amazon S3. MediaStore provides end-to-end storage APIs specifically for video, and is recommended for
performance-sensitive video workloads. 

## S3 request parallelisation and scaling

Amazon S3 is a very large distributed system. To help you take advantage of its scale, you can horizontally scale parallel 
requests to the Amazon S3 service endpoints. In addition to distributing the requests within Amazon S3, this type of scaling 
approach helps distribute the load over multiple paths through the network.

For high-throughput transfers, Amazon S3 advises using applications that use multiple connections to GET or PUT data in 
parallel. For example, this is supported by Amazon S3 Transfer Manager in the AWS Java SDK, and most of the other AWS SDKs 
provide similar constructs. For some applications, you can achieve parallel connections by launching multiple 
requests concurrently in different application threads, or in different application instances. The best approach to 
take depends on your application and the structure of the objects that you are accessing.

Amazon S3 automatically scales to high request rates. For example, your application can achieve at least 3,500 PUT/COPY/POST/DELETE 
or 5,500 GET/HEAD requests per second per prefix in a bucket. There are no limits to the number of prefixes in a bucket. 
You can increase your read or write performance by using parallelization. Amazon S3 automatically scales in 
response to sustained new request rates, dynamically optimizing performance. If Amazon S3 is optimizing for a new 
request rate, you receive a temporary HTTP 503 request response until the optimization completes. When the performance 
optimization completes, the new request rate is no longer retried.
Because Amazon S3 optimizes its prefixes for request rates, unique key naming patterns are no longer a best practice.