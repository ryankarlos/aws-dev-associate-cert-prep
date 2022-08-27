##Amazon ElastiCache 

A web service that makes it easy to deploy, operate, and scale an in-memory data store and cache in the cloud. The service improves the performance of web applications by allowing you to retrieve information from fast, managed, in-memory data stores, instead of relying entirely on slower disk-based databases. Amazon ElastiCache supports two open-source in-memory engines:

**Redis**: a fast, open source, in-memory data store and cache. Amazon ElastiCache for Redis is a Redis-compatible in-memory 
service that delivers the ease-of-use and power of Redis along with the availability, reliability and performance suitable 
for the most demanding applications. Both single-node and up to 15-shard clusters are available, enabling scalability to 
up to 3.55 TiB of in-memory data. ElastiCache for Redis is fully managed, scalable, and secure - making it an ideal 
candidate to power high-performance use cases such as Web, Mobile Apps, Gaming, Ad-Tech, and IoT.
**Memcached** - a widely adopted memory object caching system. ElastiCache is protocol compliant with Memcached, so popular 
tools that you use today with existing Memcached environments will work seamlessly with the service.

Amazon ElastiCache automatically detects and replaces failed nodes, reducing the overhead associated with self-managed 
infrastructures and provides a resilient system that mitigates the risk of overloaded databases, which slow website 
and application load times. Through integration with Amazon CloudWatch, Amazon ElastiCache provides enhanced visibility 
into key performance metrics associated with your Redis or Memcached nodes.


##Amazon DynamoDB Accelerator 

DAX is a fully managed, highly available, in-memory cache for DynamoDB that delivers up to a 10x performance improvement – from milliseconds to microseconds – 
even at millions of requests per second. DAX does all the heavy lifting required to add in-memory acceleration to your DynamoDB tables, 
without requiring developers to manage cache invalidation, data population, or cluster management. Now you can focus on 
building great applications for your customers without worrying about performance at scale. You do not need to modify 
application logic, since DAX is compatible with existing DynamoDB API calls. 
Just as with DynamoDB, you only pay for the capacity you provision.

##Amazon CloudFront 

A global content delivery network (CDN) service that accelerates delivery of your websites, APIs, video content or 
other web assets. It integrates with other Amazon Web Services products to give developers and businesses an easy way to
accelerate content to end users with no minimum usage commitments.

Amazon CloudFront can be used to deliver your entire website, including dynamic, static, streaming, and interactive 
content using a global network of edge locations. Requests for your content are automatically routed to the nearest edge 
location, so content is delivered with the best possible performance. Amazon CloudFront is optimized to work with other 
Amazon Web Services, like Amazon Simple Storage Service (Amazon S3), Amazon Elastic Compute Cloud (Amazon EC2), Amazon 
Elastic Load Balancing, and Amazon Route 53. Amazon CloudFront also works seamlessly with any non-AWS origin server, 
which stores the original, definitive versions of your files.

##Caching in API Gateway

You can enable API caching in Amazon API Gateway to cache your endpoint's responses. With caching, you can reduce the 
number of calls made to your endpoint and also improve the latency of requests to your API.

When you enable caching for a stage, API Gateway caches responses from your endpoint for a specified time-to-live 
(TTL) period, in seconds. API Gateway then responds to the request by looking up the endpoint response from the cache 
instead of making a request to your endpoint. The default TTL value for API caching is 300 seconds. The maximum TTL 
value is 3600 seconds. TTL=0 means caching is disabled.

Caching is best-effort. You can use the CacheHitCount and CacheMissCount metrics in Amazon CloudWatch to monitor requests 
that API Gateway serves from the API cache.

The maximum size of a response that can be cached is 1048576 bytes. Cache data encryption may increase the size of the 
response when it is being cached.

