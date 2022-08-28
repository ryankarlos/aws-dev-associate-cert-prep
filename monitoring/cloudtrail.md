CloudTrail is enabled by default for your AWS account. You can use Event history in the CloudTrail console to view, 
earch, download, archive, analyze, and respond to account activity across your AWS infrastructure. This includes 
activity made through the AWS Management Console, AWS Command Line Interface, and AWS SDKs and APIs.

For an ongoing record of events in your AWS account, create a trail. A trail enables CloudTrail to deliver log 
files to an Amazon S3 bucket. By default, when you create a trail in the console, the trail applies to all AWS Regions.
The trail logs events from all Regions in the AWS partition and delivers the log files to the Amazon S3 bucket that 
you specify. Additionally, you can configure other AWS services to further analyze and act upon the event data 
collected in CloudTrail logs.
By default, CloudTrail event log files are encrypted using Amazon S3 server-side encryption (SSE). You can also choose 
to encrypt your log files with an AWS Key Management Service (AWS KMS) key. You can store your log files in your 
bucket for as long as you want. You can also define Amazon S3 lifecycle rules to archive or delete log files automatically.
If you want notifications about log file delivery and validation, you can set up Amazon SNS notifications.

CloudTrail publishes log files multiple times an hour, about every five minutes. These log files contain 
API calls from services in the account that support CloudTrail. 

You can troubleshoot operational and security incidents over the past 90 days in the CloudTrail console by 
viewing Event history. You can look up events related to creation, modification, or deletion of resources 
(such as IAM users or Amazon EC2 instances) in your AWS account on a per-region basis. Events can be viewed 
and downloaded by using the AWS CloudTrail console. You can customize the view of event history in the console by 
selecting which columns are displayed and which are hidden. You can programmatically look up events by using the
AWS SDKs or AWS Command Line Interface. You can also compare the details of events in Event history side-by-side.

