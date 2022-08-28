
## ECS

Amazon Elastic Container Service (Amazon ECS) is a highly scalable and fast container management service. 
You can use it to run, stop, and manage containers on a cluster. With Amazon ECS, your containers are defined in a
task definition that you use to run an individual task or task within a service. In this context, a service is
a configuration that you can use to run and maintain a specified number of tasks simultaneously in a cluster. 
You can run your tasks and services on a serverless infrastructure that's managed by AWS Fargate. Alternatively, 
for more control over your infrastructure, you can run your tasks and services on a cluster of Amazon EC2 
instances that you manage.

To deploy applications on Amazon ECS, your application components must be configured to run in containers. A container 
is a standardized unit of software development that holds everything that your software application requires to run.
This includes relevant code, runtime, system tools, and system libraries. Containers are created from a read-only 
template that's called an image.

Images are typically built from a Dockerfile. A Dockerfile is a plaintext file that specifies all of the 
components that are included in the container. After they're built, these images are stored in a registry where they 
can be downloaded from. Then, after you download them, you can use them to run on your cluster.

A task definition is required to run Docker containers in Amazon ECS. The following are some of the parameters that 
you can specify in a task definition:

* The Docker image to use with each container in your task
* How much CPU and memory to use with each task or each container within a task
* The launch type to use, which determines the infrastructure that your tasks are hosted on
* The Docker networking mode to use for the containers in your task
* The logging configuration to use for your tasks
* Whether the task continues to run if the container finishes or fails
* The command that the container runs when it's started
* Any data volumes that are used with the containers in the task
* The IAM role that your tasks use

You can define multiple containers in a task definition. The parameters that you use depend on the launch type that you
choose for the task. Not all parameters are valid. For a list of available parameters and information about which launch 
types that they're valid for in a task definition, see Task definition parameters.

Your entire application stack doesn't need to be on a single task definition, and in most cases it isn't on a single
task definition. Your application can span multiple task definitions. You can do this by combining related containers
into their own task definitions, each representing a single component. 

After you create a task definition, you can run the task definition as a task or a service.
* A task is the instantiation of a task definition within a cluster. After you create a task definition for your 
application within Amazon ECS, you can specify the number of tasks to run on your cluster.
* An Amazon ECS service runs and maintains your desired number of tasks simultaneously in an Amazon ECS cluster.
How it works is that, if any of your tasks fail or stop for any reason, the Amazon ECS service scheduler launches
another instance based on your task definition. It does this to replace it and thereby maintain your desired 
number of tasks in the service.
  

## Data volumes in tasks

Amazon ECS supports the following data volume options for containers.

Amazon EFS volumes — Provides simple, scalable, and persistent file storage for use with your Amazon ECS tasks. With
Amazon EFS, storage capacity is elastic. It grows and shrinks automatically as you add and remove files. 
Your applications can have the storage that they need and when they need it. Amazon EFS volumes are supported for 
tasks that are hosted on Fargate or Amazon EC2 instances. 

FSx for Windows File Server volumes — Provides fully managed Microsoft Windows file servers. These file servers are 
backed by a Windows file system. When using FSx for Windows File Server together with Amazon ECS, you can provision 
your Windows tasks with persistent, distributed, shared, and static file storage. 

Windows containers on Fargate do not support this option.

Docker volumes — A Docker-managed volume that's created under /var/lib/docker/volumes on the host Amazon EC2 instance.
Docker volume drivers (also referred to as plugins) are used to integrate the volumes with external storage systems,
such as Amazon EBS. The built-in local volume driver or a third-party volume driver can be used. Docker volumes are 
only supported when running tasks on Amazon EC2 instances. Windows containers only support the use of the local driver.
To use Docker volumes, specify a dockerVolumeConfiguration in your task definition.

Bind mounts — A file or directory on the host, such as an Amazon EC2 instance or AWS Fargate, is mounted into a container. 
Bind mount host volumes are supported for tasks that are hosted on Fargate or Amazon EC2 instances.

## Container agents

The Amazon ECS container agent allows container instances to connect to your cluster. The Amazon ECS container agent
is included in the Amazon ECS-optimized AMIs, but you can also install it on any Amazon EC2 instance that supports the
Amazon ECS specification. The Amazon ECS container agent is supported on Amazon EC2 instances and external 
instances (on-premises server or VM). 

For tasks using the Fargate launch type and platform version 1.3.0 and prior, the Amazon ECS container agent is
installed on the AWS managed infrastructure used for these tasks. If you are only using tasks with the Fargate launch
type, no additional configuration is needed and the content in this topic does not apply. For tasks using the Fargate 
and platform version 1.4.0 and later (for Linux ) or 1.0.0 or later (for Windows), the Fargate container 
agent is used.

## Task Placement

When a task that uses the EC2 launch type is launched, Amazon ECS must determine where to place the task based on the 
requirements specified in the task definition, such as CPU and memory. Similarly, when you scale down the task count,
Amazon ECS must determine which tasks to terminate. You can apply task placement strategies and constraints to customize 
how Amazon ECS places and terminates tasks. Task placement strategies and constraints aren't supported for tasks using 
the Fargate launch type. By default, Fargate tasks are spread across Availability Zones. With all other tasks, 
default task placement strategies depend on whether you're running tasks manually or within a service. 

A task placement strategy is an algorithm for selecting instances for task placement or tasks for termination.
For example, Amazon ECS can select instances at random, or it can select instances such that tasks are distributed 
evenly across a group of instances.

A task placement constraint is a rule that's considered during task placement. For example, you can use constraints 
to place tasks based on Availability Zone or instance type. You can also associate attributes, which are name/value pairs,
with your container instances and then use a constraint to place tasks based on attribute.

When Amazon ECS places tasks, it uses the following process to select container instances:
* Identify the instances that satisfy the CPU, GPU, memory, and port requirements in the task definition.
* Identify the instances that satisfy the task placement constraints.
* Identify the instances that satisfy the task placement strategies.
* Select the instances for task placement.

## Scheduling tasks 

Amazon ECS provides a service scheduler for long-running tasks and applications. It also provides the ability to run 
tasks manually for batch jobs or single run tasks. Amazon ECS provides one whenever it places tasks on your cluster. 
You can specify the task placement strategies and constraints for running tasks that best meet your needs. 
For example, you can specify whether tasks run across multiple Availability Zones or within a single Availability 
Zone. And, optionally, you can integrate tasks with your own custom or third-party schedulers.

The service scheduler is suitable for long running stateless services and applications. The service scheduler ensures 
that the scheduling strategy that you specify is followed and reschedules tasks when a task fails. For example, 
if the underlying infrastructure fails, the service scheduler can reschedule tasks.

There are two service scheduler strategies available:

**REPLICA**—The replica scheduling strategy places and maintains the desired number of tasks across your cluster. 
By default, the service scheduler spreads tasks across Availability Zones. You can use task placement strategies and 
constraints to customize task placement decisions. 

**DAEMON**—The daemon scheduling strategy deploys exactly one task on each active container instance that meets all 
of the task placement constraints that you specify in your cluster. The service scheduler evaluates the task placement 
constraints for running tasks and will stop tasks that do not meet the placement constraints. When using this strategy,
there is no need to specify a desired number of tasks, a task placement strategy, or use Service Auto Scaling policies.
Fargate tasks do not support the DAEMON scheduling strategy.

The service scheduler optionally also makes sure that tasks are registered against an Elastic Load Balancing load balancer. 
You can update your services that are maintained by the service scheduler. This might include deploying a new task 
definition or changing the number of desired tasks that are running. By default, the service scheduler spreads tasks 
across multiple Availability Zones. However, you can use task placement strategies and constraints to customize task 
placement decisions. 

If you have tasks to run at set intervals in your cluster, you can use the Amazon ECS console to create an EventBridge event.
You can run tasks for a backup operation or a log scan. The EventBridge event that you create can run one or more 
tasks in your cluster at specified times. Your scheduled event can be set to a specific interval (run every N 
minutes, hours, or days). Otherwise, for more complicated scheduling, you can use a cron expression.

## Auto Scaling 

Amazon ECS can manage the scaling of Amazon EC2 instances registered to your cluster. This is referred to as 
Amazon ECS cluster auto scaling. This is done by using an Amazon ECS Auto Scaling group capacity provider with managed 
scaling turned on. When you use an Auto Scaling group capacity provider with managed scaling turned on, Amazon ECS 
creates two custom CloudWatch metrics and a target tracking scaling policy that attaches to your Auto Scaling group. 
Amazon ECS then manages the scale-in and scale-out actions of the Auto Scaling group based on the load your tasks put 
on your cluster.

### Scale-Out Behaviour 

When you have Auto Scaling group capacity providers that use managed scaling, Amazon ECS estimates the lower bound 
for the optimal number of instances to add to your cluster and uses this value to determine how many instances to request
. The following describes the scale-out behavior in more detail.

* Group all of the provisioning tasks so that each group has the same exact resource requirements.
* When you use multiple instance types in a group, the instances in the Auto Scaling group are sorted by their parameters, 
  such as vCPU, memory, elastic network interfaces (ENIs), ports, and GPUs. The smallest and the largest instance types 
  for each parameter are selected. For more information about how to choose the instance type, see Characterizing your 
  application in the Amazon ECS Best Practices Guide.
* For each group of tasks, Amazon ECS calculates the number of instances required to run the unplaced tasks. This 
  calculation uses a binpack strategy which accounts for the vCPU, memory, elastic network interfaces (ENI), ports, and 
  GPUs requirements of the tasks and the resource availability of the Amazon EC2 instances. The values for the largest 
  instance types are treated as the maximum calculated instance count. The values for the smallest instance type are used 
  as protection. If the smallest instance type cannot run at least one instance of the task, the calculation considers 
  the task as not compatible and it is excluded from scale-out calculation. When all tasks are not compatible with the
  smallest instance type, cluster Auto Scaling stops and the CapacityProviderReservation value remains at 100%.
* Amazon ECS publishes the CapacityProviderReservation metric to CloudWatch with respect to the minimumScalingStepSize 
  if the maximum calculated instance count is less than the minimum scaling step size, or the lower value of either the
  maximumScalingStepSize or the maximum calculated instance count.
* CloudWatch alarms consume the CapacityProviderReservation metric (published by Amazon ECS) for capacity providers and 
  increase the DesiredCapacity of the Auto Scaling group only when the value is greater than the targetCapacity value. 
  The targetCapacity value is a capacity provider setting that is sent to the CloudWatch alarm during the cluster auto 
  scaling activation phase.
* You can set the targetCapacity when you create the Auto Scaling group, or modify the value after the group is created. 
  The default is 100%.
* The Auto Scaling group launches additional EC2 instances. To prevent over-provisioning of the scale-out operation, 
  auto scaling stabilizes recently launched EC2 instance capacitybefore launching new instances. Auto scaling checks if 
  all existing instances have passed the instanceWarmupPeriod (now minus the instance launch time). If any instance is 
  within the instanceWarmupPeriod, Amazon ECS blocks the scale-out operation until the instanceWarmupPeriod expires.

### Scale-In Behaviour

Amazon ECS monitors container instances for each capacity provider within cluster. When at least one container instance 
does not have at least one running task it considered as empty and Amazon ECS starts scale-in process. The following 
describes the scale-in behavior in more detail.

* Amazon ECS calculates the number of empty container instances. A container instance is considered empty when there are
no no-daemon tasks running.
* Amazon ECS sets the CapcityProviderReservation value to 100 - the number of empty container instances. For example, 
if the number of empty container instances is 2, the value is set to 98%. Then, Amazon ECS publishes the metric to 
CloudWatch.
* The CapacityProviderReservation metric generates a CloudWatch alarm. This alarm updates the DesiredCapacity value for 
the Auto Scaling group. One of the following actions happens:
    1. If you do not use capacity provider managed termination, the Auto Scaling group terminates the number Auto Scaling 
    group will terminate the number of EC2 instances to reach DesiredCapacity. The container instances are then deregistered 
    from the cluster.
    2. If all the container instances use capacity provider managed termination protection, Amazon ECS removes the scale-in 
    protection on the container instances that do not have non-daemon tasks running. The Auto Scaling group will then be able to terminate the EC2 instances. The container instances are then deregistered from the cluster.
    
