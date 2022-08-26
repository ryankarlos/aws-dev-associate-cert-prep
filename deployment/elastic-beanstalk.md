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





