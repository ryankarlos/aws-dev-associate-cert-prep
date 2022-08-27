
### Explicitly declare and isolate dependencies

Code that needs to be used by multiple functions should be packaged into its own library and 
included inside your deployment package. if you find that you need to often include special 
processing or business logic, the best solution may be to try to create a purposeful library yourself.

### Store config in env variables

Lambda also allows you to encrypt these key-value pairs using KMS, such that they can be used to store secrets such as API keys or passwords for databases.
You can also use them to help define application environment specifics, such as differences between testing or production environments 
where you might have unique databases or endpoints with which your Lambda function needs to interface. You could also use these for setting A/B testing flags or to enable or disable certain function logic.

For API Gateway, these configuration variables are called stage variables. Like environment variables in Lambda, these are 
key-value pairs that are available for API Gateway to consume or pass to your API’s backend service. Stage variables can be 
useful to send requests to different backend environments based on the URL from which your API is accessed. 
For example, a single configuration could support both beta.yourapi.com vs. prod.yourapi.com. You could also use 
stage variables to pass information to a Lambda function that causes it to perform different logic.


### Execute the app as one or more stateless processes

This is inherent in how Lambda is designed so there is nothing more to consider. Lambda functions should always be 
treated as being stateless, despite the ability to potentially store some information locally between execution environment re-use.
This is because there is no guaranteed affinity to any execution environment, and the potential for an execution environment to 
go away between invocations exists. You should always store any stateful information in a database, cache, or separate data 
store via a backing service.

### Scaling and concurrency 

Lambda was built with massive concurrency and scale in mind. 

Lambda automatically scales to meet the demands of invocations sent at your function. This is in contrast to a traditional 
compute model using physical hosts, virtual machines, or containers that you self-manage. With Lambda, 
you do not need to manage overall capacity or apply scaling policies.

Each AWS account has an overall AccountLimit value that is fixed at any point in time, but can be easily increased as 
needed. As of May 2017, the default limit is 1000 concurrent executions per AWS Region. You can also set and manage a 
reserved concurrency limit, which provides a limit to how much concurrency a function can have. It also reserves 
concurrency capacity for a given function out of the total available for an account.