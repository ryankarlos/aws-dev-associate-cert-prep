AWS X-Ray is a service that collects data about requests that your application serves, and provides tools that you 
can use to view, filter, and gain insights into that data to identify issues and opportunities for optimization.
For any traced request to your application, you can see detailed information not only about the request and response,
but also about calls that your application makes to downstream AWS resources, microservices, databases, and web APIs.

AWS X-Ray receives traces from your application, in addition to AWS services your application uses that are already 
integrated with X-Ray. Instrumenting your application involves sending trace data for incoming and outbound 
requests and other events within your application, along with metadata about each request. Many instrumentation
scenarios require only configuration changes. For example, you can instrument all incoming HTTP requests and downstream 
calls to AWS services that your Java application makes. There are several SDKs, agents, and tools that can be used to 
instrument your application for X-Ray tracing.

AWS services that are integrated with X-Ray can add tracing headers to incoming requests, send trace data to X-Ray, 
or run the X-Ray daemon. For example, AWS Lambda can send trace data about requests to your Lambda functions, 
and run the X-Ray daemon on workers to make it simpler to use the X-Ray SDK.

Instead of sending trace data directly to X-Ray, each client SDK sends JSON segment documents to a daemon process listening
for UDP traffic. The X-Ray daemon buffers segments in a queue and uploads them to X-Ray in batches. The daemon is 
available for Linux, Windows, and macOS, and is included on AWS Elastic Beanstalk and AWS Lambda platforms.

X-Ray uses trace data from the AWS resources that power your cloud applications to generate a detailed service map. 
The service map shows the client, your front-end service, and backend services that your front-end service calls to 
process requests and persist data. Use the service map to identify bottlenecks, latency spikes, and other issues 
to solve to improve the performance of your applications.


## Segments

The compute resources running your application logic send data about their work as segments. A segment provides the 
resource's name, details about the request, and details about the work done. For example, when an HTTP request reaches 
your application, it can record the following data about:

* The host – hostname, alias or IP address
* The request – method, client address, path, user agent
* The response – status, content
* The work done – start and end times, subsegments
* Issues that occur – errors, faults and exceptions, including automatic capture of exception stacks.

## Subsegments

A segment can break down the data about the work done into subsegments. Subsegments provide more granular timing information
and details about downstream calls that your application made to fulfill the original request. A subsegment can contain 
additional details about a call to an AWS service, an external HTTP API, or an SQL database. You can even define arbitrary
subsegments to instrument specific functions or lines of code in your application.

For services that don't send their own segments, like Amazon DynamoDB, X-Ray uses subsegments to generate inferred 
segments and downstream nodes on the service map. This lets you see all of your downstream dependencies, even if they 
don't support tracing, or are external.

Subsegments represent your application's view of a downstream call as a client. If the downstream service is also 
instrumented, the segment that it sends replaces the inferred segment generated from the upstream client's subsegment.
The node on the service graph always uses information from the service's segment, if it's available, while the edge 
between the two nodes uses the upstream service's subsegment.

For example, when you call DynamoDB with an instrumented AWS SDK client, the X-Ray SDK records a subsegment for 
that call. DynamoDB doesn't send a segment, so the inferred segment in the trace, the DynamoDB node on the service 
graph, and the edge between your service and DynamoDB all contain information from the subsegment.

When you call another instrumented service with an instrumented application, the downstream service sends its own segment
to record its view of the same call that the upstream service recorded in a subsegment. 


## Service Graph

X-Ray uses the data that your application sends to generate a service graph. Each AWS resource that sends data to X-Ray
appears as a service in the graph. Edges connect the services that work together to serve requests. Edges connect clients
to your application, and your application to the downstream services and resources that it uses.
A service graph is a JSON document that contains information about the services and resources that make up your application. 
The X-Ray console uses the service graph to generate a visualization or service map.

## Traces

A trace ID tracks the path of a request through your application. A trace collects all the segments generated by a
single request. That request is typically an HTTP GET or POST request that travels through a load balancer, hits your
application code, and generates downstream calls to other AWS services or external web APIs. The first supported 
service that the HTTP request interacts with adds a trace ID header to the request, and propagates it downstream to
track the latency, disposition, and other request data.

## Sampling 

To ensure efficient tracing and provide a representative sample of the requests that your application serves, the X-Ray 
SDK applies a sampling algorithm to determine which requests get traced. By default, the X-Ray SDK records the first
request each second, and five percent of any additional requests.
To avoid incurring service charges when you are getting started, the default sampling rate is conservative. You can
configure X-Ray to modify the default sampling rule and configure additional rules that apply sampling based on
properties of the service or request.
For example, you might want to disable sampling and trace all requests for calls that modify state or handle user 
accounts or transactions. For high-volume read-only calls, like background polling, health checks, or connection 
maintenance, you can sample at a low rate and still get enough data to see any issues that arise.

## Tracing Header

All requests are traced, up to a configurable minimum. After reaching that minimum, a percentage of requests are 
traced to avoid unnecessary cost. The sampling decision and trace ID are added to HTTP requests in tracing headers 
named X-Amzn-Trace-Id. The first X-Ray-integrated service that the request hits adds a tracing header, which is read 
by the X-Ray SDK and included in the response.

Example Tracing header with root trace ID and sampling decision: 
```
X-Amzn-Trace-Id: Root=1-5759e988-bd862e3fe1be46a994272793;Sampled=1
```
The tracing header can also contain a parent segment ID if the request originated from an instrumented application.
For example, if your application calls a downstream HTTP web API with an instrumented HTTP client,
the X-Ray SDK adds the segment ID for the original request to the tracing header of the downstream request. 
An instrumented application that serves the downstream request can record the parent segment ID to connect the two requests.

Example Tracing header with root trace ID, parent segment ID and sampling decision:
```
X-Amzn-Trace-Id: Root=1-5759e988-bd862e3fe1be46a994272793;Parent=53995c3f42cd8ad8;Sampled=1
```

## Filter Expressions 

Even with sampling, a complex application generates a lot of data. The AWS X-Ray console provides an easy-to-navigate 
view of the service graph. It shows health and performance information that helps you identify issues and opportunities 
for optimization in your application. For advanced tracing, you can drill down to traces for individual requests, or 
use filter expressions to find traces related to specific paths or users.

## Annotations and Metadata

When you instrument your application, the X-Ray SDK records information about incoming and outgoing requests, the 
AWS resources used, and the application itself. You can add other information to the segment document as annotations and
metadata. Annotations and metadata are aggregated at the trace level, and can be added to any segment or subsegment.

**Annotations** are simple key-value pairs that are indexed for use with filter expressions. Use annotations to record
data that you want to use to group traces in the console, or when calling the GetTraceSummaries API.

X-Ray indexes up to 50 annotations per trace.

**Metadata** are key-value pairs with values of any type, including objects and lists, but that are not indexed. Use 
metadata to record data you want to store in the trace but don't need to use for searching traces.

You can view annotations and metadata in the segment or subsegment details in the X-Ray console.

## Errors, Faults and Exceptions

X-Ray tracks errors that occur in your application code, and errors that are returned by downstream services.
Errors are categorized as follows.

* Error – Client errors (400 series errors)
* Fault – Server faults (500 series errors)
* Throttle – Throttling errors (429 Too Many Requests)

When an exception occurs while your application is serving an instrumented request, the X-Ray SDK records details 
about the exception, including the stack trace, if available. You can view exceptions under segment details 
in the X-Ray console.

## XRay daemon

The AWS X-Ray daemon is a software application that listens for traffic on UDP port 2000, gathers raw segment data, 
and relays it to the AWS X-Ray API. The daemon works in conjunction with the AWS X-Ray SDKs and must be running so 
that data sent by the SDKs can reach the X-Ray service.

On AWS Lambda and AWS Elastic Beanstalk, use those services' integration with X-Ray to run the daemon. Lambda runs the 
daemon automatically any time a function is invoked for a sampled request. On Elastic Beanstalk, use the XRayEnabled 
configuration option to run the daemon on the instances in your environment.

To run the X-Ray daemon locally, on-premises, or on other AWS services, download it, run it, and then give it permission
to upload segment documents to X-Ray.