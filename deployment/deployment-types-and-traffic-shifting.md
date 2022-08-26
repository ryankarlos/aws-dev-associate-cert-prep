A deployment configuration is set of deployment rules and deployment success and failure 
conditions used by CodeDeploy during a deployment. If your deployment uses the 
EC2/On-Premises compute platform, you can specify the minimum number of healthy instances 
for the deployment. If your deployment uses the AWS Lambda or the Amazon ECS compute 
platform, you can specify how traffic is routed to your updated Lambda function or ECS task set.


### In-place deployment

The application on each instance in the deployment group is stopped, the latest application revision is installed, and the new 
version of the application is started and validated. You can use a load balancer 
so that each instance is deregistered during its deployment and then restored to 
service after the deployment is complete. Only deployments that use the EC2/On-Premises 
compute platform can use in-place deployments. For more information about in-place deployments, 
see Overview of an in-place deployment.

### Blue/green deployments

shift traffic between two identical environments that are running different 
versions of your application. The blue environment represents the current application version 
serving production traffic. In parallel, the green environment is staged running a 
different version of your application. 
Traditional deployments with in-place upgrades make it difficult to validate your new
application version in a production deployment while also continuing to run the earlier
version of the application. Blue/green deployments provide a level of isolation between
your blue and green application environments. This helps ensure spinning up a parallel
green environment does not affect resources underpinning your blue environment. This
isolation reduces your deployment risk.

The behavior of your deployment depends on which compute platform you use:

**Blue/green on an EC2/On-Premises compute platform**: 
The instances in a deployment group (the original environment) are replaced by a different
set of instances (the replacement environment) using these steps:

* Instances are provisioned for the replacement environment.
* The latest application revision is installed on the replacement instances.
* An optional wait time occurs for activities such as application testing and system verification.
* Instances in the replacement environment are registered with an Elastic Load Balancing load balancer, causing traffic to be rerouted to them. Instances in the original environment are deregistered and can be terminated or kept running for other uses.

**Blue/green on an AWS Lambda or Amazon ECS compute platform**: 
Traffic is shifted in increments accorinding to a canary, linear, or all-at-once deployment 
configuration. See next section for more details

**Blue/green deployments through AWS CloudFormation**: Traffic is shifted from your current resources 
to your updated resources as part of an AWS CloudFormation stack update. Currently, only 
ECS blue/green deployments are supported.


## Traffic shifting


Traffic is shifted in increments according to a canary, linear, or all-at-once deployment 
configuration. 

Canary: Traffic is shifted in two increments. You can choose from predefined canary options 
that specify the percentage of traffic shifted to your updated Lambda function or ECS task 
set in the first increment and the interval, in minutes, before the remaining traffic is shifted in the second increment.

Linear: Traffic is shifted in equal increments with an equal number of minutes between each 
increment. You can choose from predefined linear options that specify the percentage of 
traffic shifted in each increment and the number of minutes between each increment.

All-at-once: All traffic is shifted from the original Lambda function or ECS task set to the 
updated function or task set all at once.
