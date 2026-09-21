# Queue

Dotkernel component used to queue tasks to be processed asynchronously based on [netglue/laminas-messenger](https://github.com/netglue/laminas-messenger)

A queue system is a vital part in modern web applications that enables the decoupling of certain tasks from the regular request-response cycle.

This is especially useful for time-consuming and resource-intensive operations which are thus handled asynchronously by background workers on a separate system.

The greatest benefit is to application responsiveness, which allows faster execution, while the heavy lifting is scheduled in the queue based on available resources.

The queue system uses logs to ensure maintainability and implements retry features for reliability and stability.

![Queue process](https://docs.dotkernel.org/img/queue/schema.png)
