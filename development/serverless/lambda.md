



## Concurrency 
Lambda was built with massive concurrency and scale in mind. 

Lambda automatically scales to meet the demands of invocations sent at your function. This is in contrast to a traditional 
compute model using physical hosts, virtual machines, or containers that you self-manage. With Lambda, 
you do not need to manage overall capacity or apply scaling policies.

Each AWS account has an overall AccountLimit value that is fixed at any point in time, but can be easily increased as 
needed. As of May 2017, the default limit is 1000 concurrent executions per AWS Region. You can also set and manage a 
reserved concurrency limit, which provides a limit to how much concurrency a function can have. It also reserves 
concurrency capacity for a given function out of the total available for an account.