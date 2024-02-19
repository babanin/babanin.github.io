---
title: "How to deliver data quickly"
date: 2024-02-20T00:29:01+03:00
tags: [system design, batching]
description: ''
---

## Batching

The idea of batching is very simple, instead of sending each request or message separately we can group them together into a single request. How does this help?

There are many benefits:
* Batching increases throughput. We know that every request consumes resources. Usually it is better to make fewer requests with more data as opposed to make more requests with less data: 
  * No need to pass HTTP headers with each request
  * No need to establish multiple connections and allocate resources for each of them
  * No need to create multiple threads to process request with assumption that server is using thread-per-request model

* Fewer requests also mean fewer dollar cost - this is particularly true for public cloud services with pay per request pricing model - the fewere request we make, the less money we pay for services

* Less chance of being throttled - services often protect themselves from being abused by clients that send too many requests in a short period of time


However, batching introduces complexity on both the client and server side. 
* On client side we need to accumulate messages in a buffer before sending out and flush the buffer based on time or size (which one comes first) - all this make the client more harder to implement and configure
* On server side, messages in the request are processed one by one and partial failures are possible: first four messages were processed successfully, but on fifth - message handler failed. What should we do? Continue processing? Report failure? Should we rollback already processed messages?

It's so much easier when a single message per request was sent because in the case of failure we definitely know which message was failed and can easily retry an entire request.  

Looks like we have to options of handling batch request on server:
* **Treat the entire request as a single atomic unit** - request succeeds only when all nested operations complete successfully
* **Treach each nested operation independently and report back failures for each individual operation** - service processing the request tries to make as much progress as possible. This approach is more common in practice.

What format should we use for batch request?
* **Batch multiple requests into a single call** - combine multiple HTTP requests into one standard HTTP request. Think of it like a concatenation of individual requests. We take multiple HTTP requests with headers & bodies and then concatenate them using separator. The server process each HTTP request and returns single standard HTTP response which contains status codes for each individual request. Many Google APIs support batching and they use this format, for example Google Drive batch API: https://developers.google.com/drive/api/guides/performance#batch-requests 
* **Batch multiple resources into a single call** -  A good example of this approach is AWS SQS batch API which submits multiple messages to specified queue: https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html. In this approach instead of concatenating HTTP requests, we combine multiple messages. A server returns back a standard HTTP response that contains a list of results for each individual message. Each result in the list contains identificator of message and status - whether message was successfully queued or not.

Batch request result in combination of successfull and unsuccessfull operations. Now let's get back to client and take a look at how we can handle partial success results:
* retry the entire batch request - perfectly valid option if each individual message is idempontent. The server will replay all operations one more time and this will not have an effect on previously succedded operations but failed operations will get a chance to succeed. The big advantage of this option is that it is pretty easy to implement it on client side.

* retry each failed operation individually

* create another batch request containing only failed individual operations

The last two options require a bigger effort on the part of the client. The client has to check the each individual operation failures and create one or more new request. However, these two options doesn't require server to be idempontent.

Let's take a look on SQS batch API when retrieving messages from SQS we can configure a pull request to return multiple messages up to 10. Consumer is responsible for deleting processed messages and SQS provides a batch API to do this: delete messages batch API, where a list of message identifiers is specified in a batch request. The response of delete messages batch API contains status of deleting operation for every message. As you know already, fail to delete message will not be removed from the queue and will be retrieved by this or other consumers later when visibility timeout will be expired on such messages. So the consumer has to scan through delete messages batch response and check for individual message deletion failures. If at least one failure is identified, the consumer can use any of these three options mentioned above.

It's worth mentioning that Kafka heavily relies on batching on both sides: when producer is sending messages to a broker and consumer when retrieving messages. Batching is one of the secrets that makes Kafka very high-throughput.


## Compression