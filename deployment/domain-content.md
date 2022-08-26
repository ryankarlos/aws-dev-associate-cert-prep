Domain 1: Deployment

1.1 Deploy written code in AWS using existing CI/CD pipelines, processes, and patterns.
* Commit code to a repository and invoke build, test and/or deployment actions
* Use labels and branches for version and release management
* Use AWS CodePipeline to orchestrate workflows against different environments
* Apply AWS CodeCommit, AWS CodeBuild, AWS CodePipeline, AWS CodeStar, and AWS
CodeDeploy for CI/CD purposes
* Perform a roll back plan based on application deployment policy

1.2 Deploy applications using AWS Elastic Beanstalk.
* Utilize existing supported environments to define a new application stack
* Package the application
* Introduce a new application version into the Elastic Beanstalk environment
* Utilize a deployment policy to deploy an application version (i.e., all at once, rolling, rolling with
batch, immutable)
* Validate application health using Elastic Beanstalk dashboard
* Use Amazon CloudWatch Logs to instrument application logging

1.3 Prepare the application deployment package to be deployed to AWS.
* Manage the dependencies of the code module (like environment variables, config files and
static image files) within the package
* Outline the package/container directory structure and organize files appropriately
* Translate application resource requirements to AWS infrastructure parameters (e.g., memory,
cores)
  
1.4 Deploy serverless applications.
* Given a use case, implement and launch an AWS Serverless Application Model (AWS SAM)
template
* Manage environments in individual AWS services (e.g., Differentiate between Development,
Test, and Production in Amazon API Gateway)