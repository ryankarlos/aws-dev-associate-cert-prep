AWS Elastic Beanstalk is a fast and simple way to get an application up and running on
AWS. It’s perfect for developers who want to deploy code without worrying about
managing the underlying infrastructure. Elastic Beanstalk supports Auto Scaling and
ELB, both of which allow for blue/green deployment. Elastic Beanstalk helps you run
multiple versions of your application and provides capabilities to swap the environment
URLs, facilitating blue/green deployment.

AWS Elastic Beanstalk provides a wide range of options for customizing the resources in your environment, and Elastic Beanstalk behavior and platform settings. 
When you create a web server environment, Elastic Beanstalk creates several resources to support the operation of your application.

**EC2 instance** – An Amazon Elastic Compute Cloud (Amazon EC2) virtual machine configured to run web apps on the platform that you choose.
Each platform runs a specific set of software, configuration files, and scripts to support a specific language version, 
framework, web container, or combination of these. Most platforms use either Apache or NGINX as a reverse proxy that sits in front of your web app, forwards requests to it, serves static assets, and generates access and error logs.
**Instance security group** – An Amazon EC2 security group configured to allow inbound traffic on port 80. This resource lets HTTP traffic from the load balancer 
reach the EC2 instance running your web app. By default, traffic isn't allowed on other ports.
**Load balancer** – An Elastic Load Balancing load balancer configured to distribute requests to the instances 
running your application. A load balancer also eliminates the need to expose your instances directly to the internet.
**Load balancer security group** – An Amazon EC2 security group configured to allow inbound traffic on port 80. 
This resource lets HTTP traffic from the internet reach the load balancer. By default, traffic isn't allowed on other ports.
**Auto Scaling group** – An Auto Scaling group configured to replace an instance if it is terminated or 
becomes unavailable.
**Amazon S3 bucket** – A storage location for your source code, logs, and other artifacts that are 
created when you use Elastic Beanstalk.
**Amazon CloudWatch alarms** – Two CloudWatch alarms that monitor the load on the instances in your environment 
and that are triggered if the load is too high or too low. When an alarm is triggered, your Auto Scaling group scales 
up or down in response.
**AWS CloudFormation stack** – Elastic Beanstalk uses AWS CloudFormation to launch the resources in your environment 
and propagate configuration changes. The resources are defined in a template that you can view in the AWS CloudFormation console.
**Domain name** – A domain name that routes to your web app in the form subdomain.region.elasticbeanstalk.com.

**Worker environments**

AWS resources created for a worker environment tier include an Auto Scaling group, one or more Amazon EC2 instances, 
and an IAM role. For the worker environment tier, Elastic Beanstalk also creates and provisions an Amazon SQS queue 
if you don’t already have one. When you launch a worker environment, Elastic Beanstalk installs the necessary support 
files for your programming language of choice and a daemon on each EC2 instance in the Auto Scaling group. 
The daemon reads messages from an Amazon SQS queue. The daemon sends data from each message that it reads to the web 
application running in the worker environment for processing. If you have multiple instances in your worker environment, 
each instance has its own daemon, but they all read from the same Amazon SQS queue.


### Managing environments

AWS Elastic Beanstalk makes it easy to create new environments for your application. You can create and manage separate 
environments for development, testing, and production use, and you can deploy any version of your application to any environment. 
Environments can be long-running or temporary. When you terminate an environment, you can save its configuration to 
recreate it later.

As you develop your application, you will deploy it often, possibly to several different environments for different 
purposes. Elastic Beanstalk lets you configure how deployments are performed. You can deploy to all of the instances 
in your environment simultaneously, or split a deployment into batches with rolling deployments.

Configuration changes are processed separately from deployments, and have their own scope. For example, if you change the 
type of the EC2 instances running your application, all of the instances must be replaced. On the other hand, if you modify 
the configuration of the environment's load balancer, that change can be made in-place without interrupting service or 
lowering capacity. You can also apply configuration changes that modify the instances in your environment in batches 
with rolling configuration updates.

### Manage Platform Updates

AWS Elastic Beanstalk regularly releases platform updates to provide fixes, software updates, and new features. With 
managed platform updates, you can configure your environment to automatically upgrade to the latest version of a 
platform during a scheduled maintenance window. Your application remains in service during the update process 
with no reduction in capacity. Managed updates are available on both single-instance and load-balanced environments.

You can configure your environment to automatically apply patch version updates, or both patch and minor version updates. 
Managed platform updates don't support updates across platform branches (updates to different major versions of platform 
components such as operating system, runtime, or Elastic Beanstalk components), because these can introduce changes 
that are backward incompatible.

You can also configure Elastic Beanstalk to replace all instances in your environment during the maintenance window, 
even if a platform update isn't available. Replacing all instances in your environment is helpful if your application 
encounters bugs or memory issues when running for a long period.

Managed platform updates perform immutable environment updates to upgrade your environment to a new platform version. 
Immutable updates update your environment without taking any instances out of service or modifying your environment,
before confirming that instances running the new version pass health checks.

In an immutable update, Elastic Beanstalk deploys as many instances as are currently running with the new platform 
version. The new instances begin to take requests alongside those running the old version. If the new set of instances 
passes all health checks, Elastic Beanstalk terminates the old set of instances, leaving only instances with the 
new version.


### Configuring environments

You can add AWS Elastic Beanstalk configuration files (.ebextensions) to your web 
application's source code to configure your environment and customize the 
AWS resources that it contains. Configuration files are YAML- or JSON-formatted 
documents with a .config file extension that you place in a folder named .ebextensions and 
deploy in your application source bundle.

This example makes a simple configuration change. It modifies a configuration option to set the type of your environment's 
load balancer to Network Load Balancer.

```
option_settings:
  aws:elasticbeanstalk:environment:
    LoadBalancerType: network
```

Location – Place all of your configuration files in a single folder, named .ebextensions, in the root of your source bundle. 
Folders starting with a dot can be hidden by file browsers, so make sure that the folder is added when you create 
your source bundle. See Create an application source bundle for instructions.
Naming – Configuration files must have the .config file extension.
Formatting – Configuration files must conform to YAML or JSON specifications. When using YAML, always use spaces to indent keys at different nesting levels.
Uniqueness – Use each key only once in each configuration file.


When you use the AWS Elastic Beanstalk console to deploy a new application or an application version, you'll need to upload a source bundle. Your source bundle must meet the following requirements:
* Consist of a single ZIP file or WAR file (you can include multiple WAR files inside your ZIP file)
* Not exceed 512 MB
* Not include a parent folder or top-level directory (subdirectories are fine)


If you want to deploy a worker application that processes periodic background tasks, your 
application source bundle must also include a cron.yaml file.
You can define periodic tasks in a file named cron.yaml in your source bundle to add 
jobs to your worker environment's queue automatically at a regular interval.

For example, the following cron.yaml file creates two periodic tasks. The first one runs every 
12 hours and the second one runs at 11 PM UTC every day.

```YAML
version: 1
cron:
 - name: "backup-job"
   url: "/backup"
   schedule: "0 */12 * * *"
 - name: "audit"
   url: "/audit"
   schedule: "0 23 * * *"
```

The name must be unique for each task. The URL is the path to which the POST request is sent to trigger the job. The
schedule is a CRON expression that determines when the task runs.
When a task runs, the daemon posts a message to the environment's SQS queue with a header indicating the job that 
needs to be performed. Any instance in the environment can pick up the message and process the job.

### Deployments

AWS Elastic Beanstalk provides several options for how deployments are processed, including deployment policies (All at once, Rolling, Rolling with additional batch, Immutable, and Traffic splitting) and options that let you configure batch size and health check behavior during deployments. By default, your environment uses all-at-once deployments. If you created the environment with the EB CLI and it's a scalable environment (you didn't specify the --single option), it uses rolling deployments.

With rolling deployments, Elastic Beanstalk splits the environment's Amazon EC2 instances into batches and deploys the new version of the application to one batch at a time. It leaves the rest of the instances in the environment running the old version of the application. During a rolling deployment, some instances serve requests with the old version of the application, while instances in completed batches serve other requests with the new version. For details, see How rolling deployments work.

To maintain full capacity during deployments, you can configure your environment to launch a new batch of instances before taking any instances out of service. This option is known as a rolling deployment with an additional batch. When the deployment completes, Elastic Beanstalk terminates the additional batch of instances.

Immutable deployments perform an immutable update to launch a full set of new instances running the new version of the application in a separate Auto Scaling group, alongside the instances running the old version. Immutable deployments can prevent issues caused by partially completed rolling deployments. If the new instances don't pass health checks, Elastic Beanstalk terminates them, leaving the original instances untouched.

Traffic-splitting deployments let you perform canary testing as part of your application deployment. In a traffic-splitting deployment, Elastic Beanstalk launches a full set of new instances just like during an immutable deployment. It then forwards a specified percentage of incoming client traffic to the new application version for a specified evaluation period. If the new instances stay healthy, Elastic Beanstalk forwards all traffic to them and terminates the old ones. If the new instances don't pass health checks, or if you choose to abort the deployment, Elastic Beanstalk moves traffic back to the old instances and terminates the new ones. There's never any service interruption. For details, see How traffic-splitting deployments work.

### Health checks

AWS Elastic Beanstalk uses information from multiple sources to determine if your environment is available and processing requests from 
the Internet. An environment's health is represented by one of four colors, and is displayed on the environment overview 
page of the Elastic Beanstalk console. It's also available from the DescribeEnvironments API and by calling eb status 
with the EB CLI.

Prior to version 2 Linux platform versions, the only health reporting system was basic health. The basic health reporting
system provides information about the health of instances in an Elastic Beanstalk environment based on health checks 
performed by Elastic Load Balancing for load-balanced environments, or Amazon Elastic Compute Cloud for single-instance 
environments.

In addition to checking the health of your EC2 instances, Elastic Beanstalk also monitors the other resources in your 
environment and reports missing or incorrectly configured resources that can cause your environment to become unavailable 
to users.

Metrics gathered by the resources in your environment is published to Amazon CloudWatch in five minute intervals. 
This includes operating system metrics from EC2, request metrics from Elastic Load Balancing. You can view graphs based 
on these CloudWatch metrics on the Monitoring page of the environment console. For basic health, these metrics are not 
used to determine an environment's health.

**Load Balancer**

In a load-balanced environment, Elastic Load Balancing sends a request to each instance in an environment every 10 seconds to confirm that instances are healthy. By default, the load balancer is configured to open a TCP connection on port 80. If the instance acknowledges the connection, it is considered healthy.

**Note** Configuring a health check URL does not change the health check behavior of an environment's Auto Scaling group. An unhealthy instance is removed from the load balancer, but is not automatically replaced by Amazon EC2 Auto Scaling unless you configure Amazon EC2 Auto Scaling to use the Elastic Load Balancing health check as a basis for replacing instances

**Single instance and worker tier environment health checks**

In a single instance or worker tier environment, Elastic Beanstalk determines the instance's health by monitoring its Amazon EC2 instance status. Elastic Load Balancing health settings, including HTTP health check URLs, cannot be used in these environment types.

**Addiitonal checks**

In addition to Elastic Load Balancing health checks, Elastic Beanstalk monitors resources in your environment and changes health status to red if they fail to deploy, are not configured correctly, or become unavailable. These checks confirm that:

* The environment's Auto Scaling group is available and has a minimum of at least one instance.
* The environment's security group is available and is configured to allow incoming traffic on port 80.
* The environment CNAME exists and is pointing to the right load balancer.
* In a worker environment, the Amazon Simple Queue Service (Amazon SQS) queue is being polled at least once every three minutes.


### Enhanced Health checks

Enhanced health reporting is a feature that you can enable on your environment to allow AWS Elastic Beanstalk to gather 
additional information about resources in your environment. Elastic Beanstalk analyzes the information gathered to provide
a better picture of overall environment health and aid in the identification of issues that can cause your application 
to become unavailable.

In addition to changes in how health color works, enhanced health adds a status descriptor that provides an indicator 
of the severity of issues observed when an environment is yellow or red. When more information is available about the 
current status, you can choose the Causes button to view detailed health information on the health page.

To provide detailed health information about the Amazon EC2 instances running in your environment, Elastic Beanstalk 
includes a health agent in the Amazon Machine Image (AMI) for each platform version that supports enhanced health. 
The health agent monitors web server logs and system metrics and relays them to the Elastic Beanstalk service.
Elastic Beanstalk analyzes these metrics and data from Elastic Load Balancing and Amazon EC2 Auto Scaling to 
provide an overall picture of an environment's health.

In addition to collecting and presenting information about your environment's resources, Elastic Beanstalk monitors 
the resources in your environment for several error conditions and provides notifications to help you avoid 
failures and resolve configuration issues. Factors that influence your environment's health include the results 
of each request served by your application, metrics from your instances' operating system, and the status of the 
most recent deployment.

