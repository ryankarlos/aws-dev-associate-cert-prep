
### Explicitly declare and isolate dependencies

Code that needs to be used by multiple functions should be packaged into its own library and 
included inside your deployment package. if you find that you need to often include special 
processing or business logic, the best solution may be to try to create a purposeful library yourself.

### Store config in env variables

Environment variables provide another way to specify configuration options and credentials, and can be useful for s
scripting or temporarily setting a named profile as the default.

In lambda, you can use environment variables to adjust your function's behavior without updating code. 
An environment variable is a pair of strings that is stored in a function's version-specific configuration. 
The Lambda runtime makes environment variables available to your code and sets additional environment 
variables that contain information about the function and invocation request.
Lambda also allows you to encrypt these key-value pairs using KMS, such that they can be used to store secrets such as API keys or passwords for databases.
You can also use them to help define application environment specifics, such as differences between testing or production environments 
where you might have unique databases or endpoints with which your Lambda function needs to interface. You could also use these for setting A/B testing flags or to enable or disable certain function logic.

For API Gateway, these configuration variables are called stage variables. Like environment variables in Lambda, these are 
key-value pairs that are available for API Gateway to consume or pass to your API’s backend service. Stage variables can be 
useful to send requests to different backend environments based on the URL from which your API is accessed. 
For example, a single configuration could support both beta.yourapi.com vs. prod.yourapi.com. You could also use 
stage variables to pass information to a Lambda function that causes it to perform different logic.


### Execute the app as one or more stateless processes

We also recommend that you design all your applications as stateless as possible, 
using loosely coupled, fault-tolerant components that can be scaled out as needed.
e.g.  Lambda functions should always be treated as being stateless, despite the ability to 
potentially store some information locally between execution environment re-use. 
This is because there is no guaranteed affinity to any execution environment, and the potential for an execution environment to 
go away between invocations exists. You should always store any stateful information in a database, cache, or separate data 
store via a backing service.

### Scaling and concurrency 

When operating in a physical hardware environment, in contrast to a cloud environment,
you can approach scalability in one of either two ways. Either you can scale up through 
vertical scaling or you can scale out through horizontal scaling. The scale-up approach 
requires that you invest in powerful hardware, which can support the increasing demands of your 
business. The scale-out approach requires that you follow a distributed model of investment. 
As such, your hardware and application acquisitions can be more targeted, your data sets are 
federated, and your design is service oriented. The scale-up approach can be expensive, and there's 
also the risk that your demand could outgrow your capacity. In this regard, the scale-out approach 
is usually more effective. However, when using it, you must be able to predict demand at regular 
intervals and deploy infrastructure in chunks to meet that demand. As a result, this approach 
can often lead to unused capacity and might require some careful monitoring.

By migrating to the cloud, you can make your infrastructure align well with demand by leveraging the elasticity of cloud.
Elasticity helps to streamline resource acquisition and release. With it, your infrastructure can rapidly s
cale in and scale out as demand fluctuates. To use it, configure your Auto Scaling settings to scale 
up or down based on the metrics for the resources in your environment. For example, you can set metrics 
such as server utilization or network I/O. You can use Auto Scaling for compute capacity to be added automatically 
whenever usage rises and for it to be removed whenever usage drops. You can publish system metrics (for example, 
CPU, memory, disk I/O, and network I/O) to Amazon CloudWatch. Then, you can use CloudWatch to configure a
larms to trigger Auto Scaling actions or send notifications based on these metrics

## Fault tolerance

As a rule of thumb, you should be a pessimist when designing architecture for the cloud. Leverage the elasticity that 
it offers. Always design, implement, and deploy for automated recovery from failure. Use multiple Availability Zones 
for your Amazon EC2 instances and for Amazon RDS. Availability Zones are conceptually like logical data centers. 
Use Amazon CloudWatch to get more visibility into the health of your Elastic Beanstalk application and take 
appropriate actions in case of hardware failure or performance degradation. Configure your Auto Scaling settings 
to maintain your fleet of Amazon EC2 instances at a fixed size so that unhealthy Amazon EC2 instances are replaced 
by new ones. If you're using Amazon RDS, then set the retention period for backups, so that Amazon RDS can perform 
automated backups.