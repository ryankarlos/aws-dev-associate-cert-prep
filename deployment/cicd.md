
Continuous integration is a software development practice where members of a team use a version 
control system and frequently integrate their work to the same location, such as a main branch. 
Each change is built and verified to detect integration errors as quickly as possible. 
Continuous integration is focused on automatically building and testing code, as compared to 
continuous delivery, which automates the entire software release process up to production.

Continuous delivery is a software development methodology where the release process is automated. 
Every software change is automatically built, tested, and deployed to production. Before the final 
push to production, a person, an automated test, or a business rule decides when the final push should occur. Although every successful software change can be immediately released to production with continuous delivery, not all changes need to be released right away.

### CodeCommit 
Secure, highly scalable, managed source control service that hosts private Git repositories. CodeCommit eliminates the need for you to manage your 
own source control system or worry about scaling its infrastructure. You can use CodeCommit 
to s tore anything from code to binaries. It supports the standard functionality of Git, 
so it works seamlessly with your existing Git-based tools.

### CodeBuild

Fully managed build service in the cloud. CodeBuild compiles your source code, runs unit tests, and 
produces artifacts that are ready to deploy. CodeBuild eliminates the need to provision, manage, 
and scale your own build servers. You can add CodeBuild as a build or test action to the 
build or test stage of a pipeline in AWS CodePipeline.
When you call AWS CodeBuild to run a build, you must provide information about the build environment. A build environment 
represents a combination of operating system, programming language runtime, and tools that CodeBuild uses to run a build. 
For information about how a build environment works, see How CodeBuild works.
When you provide information to CodeBuild about the build environment, you specify the identifier of a 
Docker image in a supported repository type. These include the CodeBuild Docker image repository, publicly 
available images in Docker Hub, and Amazon Elastic Container Registry (Amazon ECR) repositories 
that your AWS account has permissions to access.
A buildspec is a collection of build commands and related settings, in YAML format, that CodeBuild uses to run a build. 
You can include a buildspec as part of the source code or you can define a buildspec when you 
create a build project. If you include a buildspec as part of the source code, 
by default, the buildspec file must be named buildspec.yml and placed in the root of your source directory.
You can override the default buildspec file name and location. For example, you can:
* Use a different buildspec file for different builds in the same repository, such as buildspec_debug.yml and buildspec_release.yml.
* Store a buildspec file somewhere other than the root of your source directory, such as config/buildspec.yml or in an S3 bucket. 
  The S3 bucket must be in the same AWS Region as your build project. Specify the buildspec file using its ARN (for example, arn:aws:s3:::my-codebuild-sample2/buildspec.yml).

You can specify only one buildspec for a build project, regardless of the buildspec file's name.


## CodeDeploy 

is a deployment service that automates application deployments to Amazon EC2 instances, 
on-premises instances, serverless Lambda functions, or Amazon ECS services.
The CodeDeploy agent is required only if you deploy to an EC2/On-Premises compute platform. The agent is not required for 
deployments that use the Amazon ECS or AWS Lambda compute platform. A configuration file is placed on the instance when the agent is installed. This file is used to specify how the agent works. 
This configuration file specifies directory paths and other settings  for AWS CodeDeploy to use as it interacts with the instance. 

An application specification file (AppSpec file), which is unique to CodeDeploy, is a YAML-formatted or JSON-formatted file. 
The AppSpec file is used to manage each deployment as a series of 
lifecycle event hooks, which are defined in the file.

For AWS Lambda compute platform applications, the AppSpec file is used by CodeDeploy to determine:
* Which Lambda function version to deploy.
* Which Lambda functions to use as validation tests.

If your application uses the EC2/On-Premises compute platform, it is used by CodeDeploy 
to determine:
* What it should install onto your instances from your application revision in Amazon S3 or GitHub.
* Which lifecycle event hooks to run in response to deployment lifecycle events.

For Amazon ECS compute platform applications, the AppSpec file is used by CodeDeploy to determine:
* Your Amazon ECS task definition file. This is specified with its ARN in the TaskDefinition instruction in the AppSpec file.
* The container and port in your replacement task set where your Application Load Balancer or Network Load Balancer 
  reroutes traffic during a deployment. This is specified with the LoadBalancerInfo instruction in the AppSpec file.
* Optional: information about your Amazon ECS service, such the platform version on which it runs, its subnets, and 
  its security groups.
* Optional: Lambda functions to run during hooks that correspond with lifecycle events during an Amazon ECS deployment. 
  For more information, see AppSpec 'hooks' section for an Amazon ECS deployment.

**Example Appspec file**

```yaml
version: 0.0
os: linux
files:
  - source: /
    destination: /var/www/html/WordPress
hooks:
  BeforeInstall:
    - location: scripts/install_dependencies.sh
      timeout: 300
      runas: root
  AfterInstall:
    - location: scripts/change_permissions.sh
      timeout: 300
      runas: root
  ApplicationStart:
    - location: scripts/start_server.sh
    - location: scripts/create_test_db.sh
      timeout: 300
      runas: root
  ApplicationStop:
    - location: scripts/stop_server.sh
      timeout: 300
      runas: root
```


## CodePipeline 

A continuous delivery service that automates the building, testing, and deployment of 
your software into production. A pipeline comprises a series of stages (e.g., build, test, and deploy), which 
act as logical divisions in your workflow. 
Each stage is made up of a sequence of actions, which are tasks such as building code or deploying to test environments.
Pipelines must have at least two stages. The first stage of a pipeline is required to be a source stage, and the 
pipeline is required to additionally have at least one other stage that is a build or deployment stage.
Define your pipeline structure through a declarative JSON document that specifies your release 
workflow and its stages and actions. These documents enable you to update existing pipelines as well as provide 
starting templates for creating new pipelines.
A revision is a change made to the source location defined for your pipeline. It can include
source code, build output, configuration, or data. A pipeline can have multiple revisions flowing through it at the same time.
Pipeline actions occur in a specified order, in serial or in parallel, as determined in the configuration of the stage.
You can add actions to your pipeline that are in an AWS Region different from your pipeline.
There are six types of actions: Source, Build, Test , Deploy, Approval , Invoke
When an action runs, it acts upon a file or set of files called artifacts. These artifacts can be worked upon by later 
actions in the pipeline. You have an artifact store which is an S3 bucket in the same AWS Region as the pipeline to 
store items for all pipelines in that Region associated with your account.
The stages in a pipeline are connected by transitions. Transitions can be disabled or enabled between stages. If all 
transitions are enabled, the pipeline runs continuously.
An approval action prevents a pipeline from transitioning to the next action until permission is granted. This is useful 
when you are performing code reviews before code is deployed to the next stage.

AWS CodePipeline can pull source code for your pipeline directly from AWS CodeCommit, GitHub, Amazon ECR, or Amazon S3.
It can run builds and unit tests in AWS CodeBuild.
It can deploy your changes using AWS CodeDeploy, AWS Elastic Beanstalk, Amazon ECS, AWS Fargate,
Amazon S3, AWS Service Catalog, AWS CloudFormation, and/or AWS OpsWorks Stacks.


