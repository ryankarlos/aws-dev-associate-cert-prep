


CloudWatch provides two categories of monitoring: basic monitoring and detailed monitoring.

Many AWS services offer basic monitoring by publishing a default set of metrics to CloudWatch with no charge to customers.
By default, when you start using one of these AWS services, basic monitoring is automatically enabled. 

Detailed monitoring is offered by only some services:
* Amazon API Gateway
* Amazon CloudFront
* Amazon EC2
* Elastic Beanstalk
* Amazon Kinesis Data Streams
* Amazon MSK
* Amazon S3

It also incurs charges. To use it for an AWS service, you must choose to activate it. 
Detailed monitoring options differ based on the services that offer it. For example, Amazon EC2 detailed monitoring 
provides more frequent metrics, published at one-minute intervals, instead of the five-minute intervals used in 
Amazon EC2 basic monitoring. Detailed monitoring for Amazon S3 and Amazon Managed Streaming for Apache Kafka means more
fine-grained metrics. In different AWS services, detailed monitoring also has different names. For example, in Amazon EC2 it is called
detailed monitoring, in AWS Elastic Beanstalk it is called enhanced monitoring, and in Amazon S3 it is called request metrics.
Using detailed monitoring for Amazon EC2 helps you better manage your Amazon EC2 resources, so that you can find 
trends and take action faster. For Amazon S3 request metrics are available at one-minute intervals to help you 
quickly identify and act on operational issues. On Amazon MSK, when you enable the PER_BROKER, PER_TOPIC_PER_BROKER, 
or PER_TOPIC_PER_PARTITION level monitoring, you get additional metrics that provide more visibility.


## Metrics

Metrics are the fundamental concept in CloudWatch. A metric represents a time-ordered set of data points that are 
published to CloudWatch. Think of a metric as a variable to monitor, and the data points as representing the values of 
that variable over time. For example, the CPU usage of a particular EC2 instance is one metric provided by Amazon EC2.
The data points themselves can come from any application or business activity from which you collect data.

By default, many AWS services provide metrics at no charge for resources (such as Amazon EC2 instances, Amazon EBS volumes,
and Amazon RDS DB instances). For a charge, you can also enable detailed monitoring for some resources, such as your 
Amazon EC2 instances, or publish your own application metrics. For custom metrics, you can add the data points in any 
order, and at any rate you choose. You can retrieve statistics about those data points as an ordered set of time-series data.

Metrics exist only in the Region in which they are created. Metrics cannot be deleted, but they automatically expire 
after 15 months if no new data is published to them. Data points older than 15 months expire on a rolling basis; as 
new data points come in, data older than 15 months is dropped.

Metrics are uniquely defined by a name, a namespace, and zero or more dimensions. Each data point in a metric has a time
stamp, and (optionally) a unit of measure. You can retrieve statistics from CloudWatch for any metric.

## Alarms 

You can use an alarm to automatically initiate actions on your behalf. An alarm watches a single metric over a specified
time period, and performs one or more specified actions, based on the value of the metric relative to a threshold over time.
The action is a notification sent to an Amazon SNS topic or an Auto Scaling policy. You can also add alarms to dashboards.

Alarms invoke actions for sustained state changes only. CloudWatch alarms do not invoke actions simply because they are 
in a particular state. The state must have changed and been maintained for a specified number of periods.

When creating an alarm, select an alarm monitoring period that is greater than or equal to the metric's 
resolution. For example, basic monitoring for Amazon EC2 provides metrics for your instances every 5 minutes. When setting 
an alarm on a basic monitoring metric, select a period of at least 300 seconds (5 minutes). Detailed monitoring for 
Amazon EC2 provides metrics for your instances with a resolution of 1 minute. When setting an alarm on a detailed monitoring metric, 
select a period of at least 60 seconds (1 minute).

If you set an alarm on a high-resolution metric, you can specify a high-resolution alarm with a period of 10 seconds 
or 30 seconds, or you can set a regular alarm with a period of any multiple of 60 seconds. There is a higher charge for 
high-resolution alarms

You can create metric and composite alarms in Amazon CloudWatch.

* A metric alarm watches a single CloudWatch metric or the result of a math expression based on CloudWatch metrics. 
  The alarm performs one or more actions based on the value of the metric or expression relative to a threshold over a 
  number of time periods. The action can be sending a notification to an Amazon SNS topic, performing an Amazon EC2 action 
  or an Amazon EC2 Auto Scaling action, or creating an OpsItem or incident in Systems Manager.
* A composite alarm includes a rule expression that takes into account the alarm states of other alarms that you have 
  created. The composite alarm goes into ALARM state only if all conditions of the rule are met. The alarms specified in a 
  composite alarm's rule expression can include metric alarms and other composite alarms.

Using composite alarms can reduce alarm noise. You can create multiple metric alarms, and also create a composite a
larm and set up alerts only for the composite alarm. For example, a composite might go into ALARM state only when all 
of the underlying metric alarms are in ALARM state.


## CloudWatch Agent

The unified CloudWatch agent enables you to do the following:

* Collect internal system-level metrics from Amazon EC2 instances across operating systems. The metrics can include 
  in-guest metrics, in addition to the metrics for EC2 instances. The additional metrics that can be collected are 
  listed in Metrics collected by the CloudWatch agent.
* Collect system-level metrics from on-premises servers. These can include servers in a hybrid environment as well as 
  servers not managed by AWS.
* Retrieve custom metrics from your applications or services using the StatsD and collectd protocols. StatsD is 
  supported on both Linux servers and servers running Windows Server. collectd is supported only on Linux servers.
* Collect logs from Amazon EC2 instances and on-premises servers, running either Linux or Windows Server.

You can store and view the metrics that you collect with the CloudWatch agent in CloudWatch just as you can with any other
CloudWatch metrics. The default namespace for metrics collected by the CloudWatch agent is CWAgent, although you can 
specify a different namespace when you configure the agent.

The logs collected by the unified CloudWatch agent are processed and stored in Amazon CloudWatch Logs, just like logs
collected by the older CloudWatch Logs agent.

**Installation Process**

You can download and install the CloudWatch agent manually using the command line, or you can integrate it with SSM. 
The general flow of installing the CloudWatch agent using either method is as follows:

1. Create IAM roles or users that enable the agent to collect metrics from the server and optionally to integrate with 
   AWS Systems Manager.
2. Download the agent package.
3. Modify the CloudWatch agent configuration file and specify the metrics that you want to collect.
4. Install and start the agent on your servers. As you install the agent on an EC2 instance, you attach the IAM role 
   that you created in step 1. As you install the agent on an on-premises server, you specify a named profile that 
   contains the credentials of the IAM user that you created in step 1.