## SAM

The AWS Serverless Application Model (AWS SAM) is an open-source framework that you can use to build serverless 
applications on AWS.

A serverless application is a combination of Lambda functions, event sources, and other resources that work 
together to perform tasks. Note that a serverless application is more than just a Lambda function—it can include 
additional resources such as APIs, databases, and event source mappings.

You can use AWS SAM to define your serverless applications.

An AWS SAM template file closely follows the format of an AWS CloudFormation template file, which is described in Template anatomy in the AWS CloudFormation User Guide. The primary differences between AWS SAM template files and AWS CloudFormation template files are the following:

**Transform declaration** The declaration Transform: AWS::Serverless-2016-10-31 is required for AWS SAM template files. This declaration identifies an AWS CloudFormation template file as an AWS SAM template file. For more information about transforms, see Transform in the AWS CloudFormation User Guide.
**Globals section** The Globals section is unique to AWS SAM. It defines properties that are common to all your serverless functions and APIs. All the AWS::Serverless::Function, AWS::Serverless::Api, and AWS::Serverless::SimpleTable resources inherit the properties that are defined in the Globals section. For more information about this section, see Globals section of the AWS SAM template.
**Resources section** In AWS SAM templates the Resources section can contain a combination of AWS CloudFormation resources and AWS SAM resources. For more information about AWS CloudFormation resources, see AWS resource and property types reference in the AWS CloudFormation User Guide. For more information about AWS SAM resources, see AWS SAM resource and property reference.
**Parameters section** Objects that are declared in the Parameters section cause the sam deploy --guided command to present additional prompts to the user. For examples of declared objects and the corresponding prompts, see sam deploy in the AWS SAM CLI command reference.

All other sections of an AWS SAM template file correspond to the AWS CloudFormation template file section of the same name.


# Manage Environments


**API Gateway**

With deployment stages in API Gateway, you can manage multiple release stages for each API, such as alpha, beta, and 
production. Using stage variables you can configure an API deployment stage to interact with different backend endpoints.

For example, your API can pass a GET request as an HTTP proxy to the backend web host (for example, http://example.com). 
In this case, the backend web host is configured in a stage variable so that when developers call your production endpoint,
API Gateway calls example.com. When you call your beta endpoint, API Gateway uses the value configured in the stage 
variable for the beta stage, and calls a different web host (for example, beta.example.com). Similarly, stage variables 
can be used to specify a different AWS Lambda function name for each stage in your API.

You can also use stage variables to pass configuration parameters to a Lambda function through your mapping templates. 
For example, you might want to reuse the same Lambda function for multiple stages in your API, but the function 
should read data from a different Amazon DynamoDB table depending on which stage is being called. In the mapping 
templates that generate the request for the Lambda function, you can use stage variables to pass the table name to Lambda.

**Lambda**

You can use environment variables to customize function behavior in your test environment and production environment. 
For example, you can create two functions with the same code but different configurations. One function connects to a 
test database, and the other connects to a production database. In this situation, you use environment variables to 
tell the function the hostname and other connection details for the database.

If you want your test environment to generate more debug information than the production environment, 
you could set an environment variable to configure your test environment to use more verbose logging or more detailed 
tracing.

You can use versions to manage the deployment of your functions. For example, you can publish a new version of a 
function for beta testing without affecting users of the stable production version. Lambda creates a new version of 
your function each time that you publish the function. The new version is a copy of the unpublished version of the function.

**Note** Lambda doesn't create a new version if the code in the unpublished version is the same as the previous published version. 
You need to deploy code changes in $LATEST before you can create a new version.

A function version includes the following information:

* The function code and all associated dependencies.
* The Lambda runtime that invokes the function.
* All the function settings, including the environment variables.
* A unique Amazon Resource Name (ARN) to identify the specific version of the function.



You can change the function code and settings only on the unpublished version of a function. When you publish a 
version, Lambda locks the code and most of the settings to maintain a consistent experience for users of that 
version. 

To publish a version of a function, use the PublishVersion API operation.

You can reference your Lambda function using either a qualified ARN or an unqualified ARN.

**Qualified ARN** – The function ARN with a version suffix. The following example refers to 
version 42 of the helloworld function.
```
arn:aws:lambda:aws-region:acct-id:function:helloworld:42
```
**Unqualified ARN** – The function ARN without a version suffix.

```
arn:aws:lambda:aws-region:acct-id:function:helloworld
```
You can use a qualified or an unqualified ARN in all relevant API operations. However, you can't use an unqualified 
ARN to create an alias.
If you decide not to publish function versions, you can invoke the function using either the qualified or unqualified 
ARN in your event source mapping. When you invoke a function using an unqualified ARN, Lambda implicitly invokes $LATEST.
Lambda publishes a new function version only if the code has never been published or if the code has changed from 
the last published version. If there is no change, the function version remains at the last published version.

The qualified ARN for each Lambda function version is unique. After you publish a version, you can't change the ARN or
the function code.


You can create one or more aliases for your Lambda function. A Lambda alias is like a pointer to a specific function 
version. Users can access the function version using the alias Amazon Resource Name (ARN).
Each alias has a unique ARN. An alias can point only to a function version, not to another alias. You can update an 
alias to point to a new version of the function.
Event sources such as Amazon Simple Storage Service (Amazon S3) invoke your Lambda function. These event sources 
maintain a mapping that identifies the function to invoke when events occur. If you specify a Lambda function alias in 
the mapping configuration, you don't need to update the mapping when the function version changes. 

In a resource policy, you can grant permissions for event sources to use your Lambda function. If you specify an alias 
ARN in the policy, you don't need to update the policy when the function version changes.


