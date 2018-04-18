# SQS Notes

SQS stands for Amazon **S**imple **Q**ueue **S**ervice.

SQS is a message queuing service.

Used for:

+ Microservices: Applications by individual components that each perform a discrete function. SQS helps send/store/receive messages between these components.
+ Distributed systems
+ Serverless applications

Similar products w/ comparisons:

+ Amazon SQS: Simple APIs.
+ Amazon SNS: Simple APIs.
+ Amazon MQ: Large projects. Migrating from other MQs.

SQS message queues:

+ Standard 
	+ At-least-once message delivery
	+ May not preserve the original 
	+ Unlimited Throughput 
+ FIFO queue
	+ Exactly-once message processing
	+ First-In-First-Out Delivery
	+ High Throughput 