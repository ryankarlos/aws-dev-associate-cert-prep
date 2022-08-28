



## Asynchronous vs Synchronous 

When you invoke a function, you can choose to invoke it synchronously or asynchronously. With synchronous invocation, 
you wait for the function to process the event and return a response. 
With asynchronous invocation, Lambda queues the event for processing and returns a response immediately.
For asynchronous invocations, Lambda handles retries if the function returns an error or is throttled. 
To customize this behavior, you can configure error handling settings on a function, version, or alias. 
You can also configure Lambda to send events that failed processing to a dead-letter queue, or to send a 
record of any invocation to a destination.

