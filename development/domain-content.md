Domain 3: Development with AWS Services

3.1 Write code for serverless applications.
* Compare and contrast server-based vs. serverless model (e.g., micro services, stateless nature
of serverless applications, scaling serverless applications, and decoupling layers of serverless
applications)
* Configure AWS Lambda functions by defining environment variables and parameters (e.g.,
memory, time out, runtime, handler)
* Create an API endpoint using Amazon API Gateway
* Create and test appropriate API actions like GET, POST using the API endpoint
* Apply Amazon DynamoDB concepts (e.g., tables, items, and attributes)
* Compute read/write capacity units for Amazon DynamoDB based on application requirements
* Associate an AWS Lambda function with an AWS event source (e.g., Amazon API Gateway,
Amazon CloudWatch event, Amazon S3 events, Amazon Kinesis)
* Invoke an AWS Lambda function synchronously and asynchronously

3.2 Translate functional requirements into application design.
* Determine real-time vs. batch processing for a given use case
* Determine use of synchronous vs. asynchronous for a given use case
* Determine use of event vs. schedule/poll for a given use case
* Account for tradeoffs for consistency models in an application design

3.3 Implement application design into application code.
* Write code to utilize messaging services (e.g., SQS, SNS)
* Use Amazon ElastiCache to create a database cache
* Use Amazon DynamoDB to index objects in Amazon S3
* Write a stateless AWS Lambda function
* Write a web application with stateless web servers (Externalize state)

3.4 Write code that interacts with AWS services by using APIs, SDKs, and AWS CLI.
* Choose the appropriate APIs, software development kits (SDKs), and CLI commands for the
code components
* Write resilient code that deals with failures or exceptions (i.e., retries with exponential back off
and jitter)