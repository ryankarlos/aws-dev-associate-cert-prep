Lambda is an ideal compute service for many application scenarios, as long as you can run your application code using the Lambda standard runtime environment and within the resources that Lambda provides.

When using Lambda, you are responsible only for your code. Lambda manages the compute fleet that offers a balance of 
memory, CPU, network, and other resources to run your code. Because Lambda manages these resources, you cannot log in to compute instances or customize the operating system on provided runtimes. Lambda performs operational and administrative activities on your behalf, including managing capacity, monitoring, and logging your Lambda functions.
If you need to manage your own compute resources, AWS has other compute services to meet your needs. For example:
* Amazon Elastic Compute Cloud (Amazon EC2) offers a wide range of EC2 instance types to choose from. It lets you 
  customize operating systems, network and security settings, and the entire software stack. You are responsible for 
  provisioning capacity, monitoring fleet health and performance, and using Availability Zones for fault tolerance.
* AWS Elastic Beanstalk enables you to deploy and scale applications onto Amazon EC2. You retain ownership and full c
  ontrol over the underlying EC2 instances.

## Execution enviornment

Lambda invokes your function in an execution environment, which provides a secure and isolated runtime environment. 
The execution environment manages the resources required to run your function. The execution environment also 
provides lifecycle support for the function's runtime and any external extensions associated with your function.

**Init phase**
In the Init phase, Lambda performs three tasks:

* Start all extensions (Extension init)
* Bootstrap the runtime (Runtime init)
* Run the function's static code (Function init)

The Init phase ends when the runtime and all extensions signal that they are ready by sending a Next API request. 
The Init phase is limited to 10 seconds. If all three tasks do not complete within 10 seconds, Lambda retries the 
Init phase at the time of the first function invocation.


**Invoke phase**

When a Lambda function is invoked in response to a Next API request, Lambda sends an Invoke event to the runtime 
and to each extension.
The function's timeout setting limits the duration of the entire Invoke phase. For example, if you set the function 
timeout as 360 seconds, the function and all extensions need to complete within 360 seconds. Note that there 
is no independent post-invoke phase. The duration is the sum of all invocation time (runtime + extensions) and 
is not calculated until the function and all extensions have finished executing.

The invoke phase ends after the runtime and all extensions signal that they are done by sending a Next API request.

If the Lambda function crashes or times out during the Invoke phase, Lambda resets the execution environment.
The reset behaves like a Shutdown event. First, Lambda shuts down the runtime. Then Lambda sends a Shutdown event to 
each registered external extension. The event includes the reason for the shutdown. If another Invoke event results 
in this execution environment being reused, Lambda initializes the runtime and extensions as part of the next invocation.

The Lambda reset does not clear the /tmp directory content prior to the next init phase. This behavior is
consistent with the regular shutdown phase.

**Shutdown Phase**

When Lambda is about to shut down the runtime, it sends a Shutdown event to each registered external extension.
Extensions can use this time for final cleanup tasks. The Shutdown event is a response to a Next API request.

The entire Shutdown phase is capped at 2 seconds. If the runtime or any extension does not respond, Lambda 
terminates it via a signal (SIGKILL).

After the function and all extensions have completed, Lambda maintains the execution environment for some time 
in anticipation of another function invocation. In effect, Lambda freezes the execution environment. When the 
function is invoked again, Lambda thaws the environment for reuse. Reusing the execution environment has the following 
implications:

* Objects declared outside of the function's handler method remain initialized, providing additional optimization when 
the function is invoked again. For example, if your Lambda function establishes a database connection, instead of 
reestablishing the connection, the original connection is used in subsequent invocations. We recommend adding logic in 
your code to check if a connection exists before creating a new one.
* Each execution environment provides 512 MB and 10,240 MB, in 1-MB increments, of disk space in the /tmp directory. 
The directory content remains when the execution environment is frozen, providing a transient cache that can be
used for multiple invocations. You can add extra code to check if the cache has the data that you stored.
* Background processes or callbacks that were initiated by your Lambda function and did not complete when the function 
ended resume if Lambda reuses the execution environment. Make sure that any background processes or callbacks in your 
code are complete before the code exits.

When you write your function code, do not assume that Lambda automatically reuses the execution environment for 
subsequent function invocations. Other factors may dictate a need for Lambda to create a new execution environment, 
which can lead to unexpected results, such as database connection failure

## Lambda layers

A Lambda layer is a .zip file archive that can contain additional code or other content. A layer can contain libraries, 
a custom runtime, data, or configuration files.

Layers provide a convenient way to package libraries and other dependencies that you can use with your Lambda 
functions. Using layers reduces the size of uploaded deployment archives and makes it faster to deploy your code. 
Layers also promote code sharing and separation of responsibilities so that you can iterate faster on writing business 
logic.
You can include up to five layers per function. Layers count towards the standard Lambda deployment size quotas. 
When you include a layer in a function, the contents are extracted to the /opt directory in the execution environment.

By default, the layers that you create are private to your AWS account. You can choose to share a layer with other 

Functions deployed as a container image do not use layers. Instead, you package your preferred runtime, libraries, 
and other dependencies into the container image when you build the image.


## Event Source Mapping 

To process items from a stream or queue, you can create an event source mapping. An event source mapping is a 
resource in Lambda that reads items from an Amazon Simple Queue Service (Amazon SQS) queue, an Amazon Kinesis stream, 
or an Amazon DynamoDB stream, and sends the items to your function in batches. Each event that your function processes 
can contain hundreds or thousands of items.

Event source mappings maintain a local queue of unprocessed items and handle retries if the function returns an error or 
is throttled. You can configure an event source mapping to customize batching behavior and error handling, or to send a 
record of items that fail processing to a destination.

## Concurrency

The first time you invoke your function, AWS Lambda creates an instance of the function and runs its
handler method to process the event. When the function returns a response, it stays active and waits to process additional events. If you invoke the function again while the first event is being processed, Lambda initializes another instance, and the function processes the two events concurrently. As more events come in, Lambda routes them to available instances and creates new instances as needed. When the number of requests decreases, Lambda stops unused instances to free up scaling capacity for other functions.

The default regional concurrency quota starts at 1,000 instances. For more information, or to request an 
increase on this quota, see Lambda quotas. To allocate capacity on a per-function basis, you can configure 
functions with reserved concurrency.

Your functions' concurrency is the number of instances that serve requests at a given 
time. For an initial burst of traffic, your functions' cumulative concurrency in a 
Region can reach an initial level of between 500 and 3000, which varies per Region. 
Note that the burst concurrency quota is not per-function; it applies to all your 
functions in the Region.

Burst concurrency quotas

* 3000 – US West (Oregon), US East (N. Virginia), Europe (Ireland)
* 1000 – Asia Pacific (Tokyo), Europe (Frankfurt), US East (Ohio)
* 500 – Other Regions

After the initial burst, your functions' concurrency can scale by an additional 500 instances each minute. 
This continues until there are enough instances to serve all requests, or until a concurrency limit is reached. 
When requests come in faster than your function can scale, or when your function is at maximum concurrency, additional 
requests fail with a throttling error (429 status code).

 As invocations increase exponentially, the function scales up. It initializes a new instance for any request that can't be routed to an available instance. 
When the burst concurrency limit is reached, the function starts to scale linearly. If this isn't enough 
concurrency to serve all requests, additional requests are throttled and should be retried.
The function continues to scale until the account's concurrency limit for the function's Region is reached. The function 
catches up to demand, requests subside, and unused instances of the function are stopped after being idle for some time.
Unused instances are frozen while they're waiting for requests and don't incur any charges.

When your function scales up, the first request served by each instance is impacted by the time it takes to load and
initialize your code. If your initialization code takes a long time, the impact on average and percentile latency 
can be significant. To enable your function to scale without fluctuations in latency, use provisioned concurrency. 

When you configure a number for provisioned concurrency, Lambda initializes that number of execution environments.
Your function is ready to serve a burst of incoming requests with very low latency. Note that configuring 
provisioned concurrency incurs charges to your AWS account.

When all provisioned concurrency is in use, the function scales up normally to handle any additional requests.

Application Auto Scaling takes this a step further by providing autoscaling for provisioned concurrency. With 
Application Auto Scaling, you can create a target tracking scaling policy that adjusts provisioned concurrency 
levels automatically, based on the utilization metric that Lambda emits. Use the Application Auto Scaling API to 
register an alias as a scalable target and create a scaling policy.

In the following example, a function scales between a minimum and maximum amount of provisioned concurrency based on 
utilization. When the number of open requests increases, Application Auto Scaling increases provisioned concurrency in 
large steps until it reaches the configured maximum. The function continues to scale on standard concurrency until 
utilization starts to drop. When utilization is consistently low, Application Auto Scaling decreases provisioned 
concurrency in smaller periodic steps.


## Deploying 

You can deploy code to your Lambda function by uploading a zip file archive, or by creating and uploading a container image.

### .zip file archives 

A .zip file archive includes your application code and its dependencies. When you author functions using the Lambda console 
or a toolkit, Lambda automatically creates a .zip file archive of your code.

When you create functions with the Lambda API, command line tools, or the AWS SDKs, you must create a deployment package. 
You also must create a deployment package if your function uses a compiled language, or to add dependencies to your
function. To deploy your function's code, you upload the deployment package from Amazon Simple Storage Service 
(Amazon S3) or your local machine.

You can upload a .zip file as your deployment package using the Lambda console, AWS Command Line Interface (AWS CLI), 
or to an Amazon Simple Storage Service (Amazon S3) bucket.

When you use a .zip file archive for the deployment package, you choose a runtime when you create the function. 
To change or upgrade the runtime, you can update your function's configuration. The runtime is paired with one of 
the Amazon Linux distributions. The underlying execution environment provides additional libraries and environment 
variables that you can access from your function code.

### Container Images

You can package your code and dependencies as a container image using tools such as the Docker command line interface (CLI). 
You can then upload the image to your container registry hosted on Amazon Elastic Container Registry (Amazon ECR).

AWS provides a set of open-source base images that you can use to build the container image for your function code. 
You can also use alternative base images from other container registries. AWS also provides an open-source runtime 
client that you add to your alternative base image to make it compatible with the Lambda service.

For a function defined as a container image, you choose a runtime and the Linux distribution when you create the container
image. To change the runtime, you create a new container image.

