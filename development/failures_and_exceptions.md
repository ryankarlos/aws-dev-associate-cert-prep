Numerous components on a network, such as DNS servers, switches, load balancers, and others can generate errors anywhere 
in the life of a given request. The usual technique for dealing with these error responses in a networked environment is
to implement retries in the client application. This technique increases the reliability of the application and reduces 
operational costs for the developer.

Each AWS SDK implements automatic retry logic. The AWS SDK for Java automatically retries requests, and you can configure 
the retry settings using the ClientConfiguration class. For example, you might want to turn off the retry logic for a web 
page that makes a request with minimal latency and no retries. Use the ClientConfiguration class and provide a maxErrorRetry 
value of 0 to turn off the retries.

If you're not using an AWS SDK, you should retry original requests that receive server (5xx) or throttling errors. 
However, client errors (4xx) indicate that you need to revise the request to correct the problem before trying again.

In addition to simple retries, each AWS SDK implements exponential backoff algorithm for better flow control. The idea 
behind exponential backoff is to use progressively longer waits between retries for consecutive error responses. 
You should implement a maximum delay interval, as well as a maximum number of retries. The maximum delay interval 
and maximum number of retries are not necessarily fixed values, and should be set based on the operation being performed,
as well as other local factors, such as network latency.

Most exponential backoff algorithms use jitter (randomized delay) to prevent successive collisions. Because you aren't
trying to avoid such collisions in these cases, you don't need to use this random number. However, if you use concurrent
clients, jitter can help your requests succeed faster. 