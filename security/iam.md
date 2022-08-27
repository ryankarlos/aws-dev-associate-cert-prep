
## Require human users to use federation with an identity provider to access AWS using temporary credentials
Human users, also known as human identities, are the people, administrators, developers, operators, and consumers 
of your applications. They must have an identity to access your AWS environments and applications. Human users that are 
members of your organization are also known as workforce identities. Human users can also be external users with whom 
you collaborate, and who interact with your AWS resources. They can do this via a web browser, client application, 
mobile app, or interactive command-line tools.
Require your human users to use temporary credentials when accessing AWS. You can use an identity provider for your 
human users to provide federated access to AWS accounts by assuming roles, which provide temporary credentials. 
For centralized access management, we recommend that you use AWS IAM Identity Center (successor to AWS Single Sign-On) 
(IAM Identity Center) to manage access to your accounts and permissions within those accounts.

## Require workloads to use temporary credentials with IAM roles to access AWS
A workload is a collection of resources and code that delivers business value, such as an application or backend process. 
Your workload can have applications, operational tools, and components that require an identity to make requests to 
AWS services, such as requests to read data. These identities include machines running in your AWS environments, such as 
Amazon EC2 instances or AWS Lambda functions.
You can also manage machine identities for external parties who need access. To give access to machine identities, 
you can use IAM roles. IAM roles have specific permissions and provide a way to access AWS by relying on temporary security 
credentials with a role session. 


## Safeguard root credentials and do not sue them for everyday tasks

When you create an AWS account you establish a root user name and password to sign in to the AWS Management Console. 
Safeguard your root user credentials the same way you would protect other sensitive personal information. You can do 
this by configuring MFA for your root user credentials. We don't recommend generating access keys for your root user, 
because they allow full access to all your resources for all AWS services, including your billing information. Don’t 
use your root user for everyday tasks. 
Use the root user to complete the tasks that only the root user can perform. 

## Rotate access keys regularly for use cases that require long-term credentials

Where possible, we recommend relying on temporary credentials instead of creating 
long-term credentials such as access keys. However, for scenarios in which you 
need IAM users with programmatic access and long-term credentials, we recommend 
that you rotate access keys. Regularly rotating long-term credentials helps you 
familiarize yourself with the process. This is useful in case you are ever in a 
situation where you must rotate credentials, such as when an employee leaves 
your company. We recommend that you use IAM access last used information to 
rotate and remove access keys safely.

## least-privilege permissions

When you set permissions with IAM policies, grant only the permissions required 
to perform a task. You do this by defining the actions that can be taken on 
specific resources under specific conditions, also known as least-privilege 
permissions. You might start with broad permissions while you explore the 
permissions that are required for your workload or use case. As your use case 
matures, you can work to reduce the permissions that you grant to work toward 
least privilege.


### MFA

We recommend using IAM roles for human users and workloads that access your AWS 
\resources so that they use temporary credentials. However, for scenarios in which
you need IAM or root users in your account, require MFA for additional security. 
With MFA, users have a device that generates a response to an authentication challenge. 
Each user's credentials and device-generated response are required to complete the
sign-in process.

## Use conditions in IAM policies to further restrict access
You can specify conditions under which a policy statement is in effect. That way, you can grant access to actions and 
resources, but only if the access request meets specific conditions. For example, you can write a policy condition to
specify that all requests must be sent using SSL. You can also use conditions to grant access to service actions, but 
only if they are used through a specific AWS service, such as AWS CloudFormation.


## Use IAM Access Analyzer to validate your IAM policies to ensure secure and functional permissions

Validate the policies you create to ensure that they adhere to the IAM policy language (JSON) and IAM best practices. 
You can validate your policies by using IAM Access Analyzer policy validation. IAM Access Analyzer provides more than 100 
policy checks and actionable recommendations to help you author secure and functional policies. As you author new 
policies or edit existing policies in the console, IAM Access Analyzer provides recommendations to help you refine and 
validate your policies before you save them. Additionally, we recommend that you review and validate all of your 
existing policies.

## Verify public and cross-account access to resources with IAM Access Analyzer
Before you grant permissions for public or cross-account access in AWS, we recommend that you verify if such access is 
required. You can use IAM Access Analyzer to help you preview and analyze public and cross-account access for supported
resource types. You do this by reviewing the findings that IAM Access Analyzer generates. These findings help you verify 
that your resource access controls grant the access that you expect. Additionally, as you update public and cross-account 
permissions, you can verify the effect of your changes before deploying new access controls to your resources. IAM Access 
Analyzer also monitors supported resource types continuously and generates a finding for resources that allow public or 
cross-account access. 