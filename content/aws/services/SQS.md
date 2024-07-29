---
tags:
  - sqs
  - queue
---
## SQS Features
- When requesting messages from a queue, you cannot specify individual messages. Instead, you determine the `maximum number of messages` you wish to retrieve, up to a limit of `10`.
- Message can contain up to `256 KB` including the attributes. 
- Use [[S3]] for messages larger than `256 KB`.
- Default visibility of an SQS message is `30 seconds` and max `12 hours`.
- Delay Queue allows you to make messages invisible for `0 - 900 seconds`.
- Messages are not automatically deleted. (Must be explicitly deleted)
- Debug SQS with [[X-Ray]] service.
	- Use the `X-Amzn-Trace-Id`
	- Use the `AWSTraceHeader`
- `ReceiptId` is about who received it and it changes for different receivers.
- Message attributes
	- Use message attributes to attach custom metadata.
	- Attributes
		- Name
		- Type
		- Value
	- Supported data types
		- String, Number, Binary, Custom
- Queue types
	- Standard queue
	- `FIFO` queue
		- FIFO queues follow exactly-once processing. It introduces a parameter called `MessageDeduplicationID`, which is the token used for deduplication of sent messages.
		- Any messages sent with the same `MessageDeduplicationID` are accepted successfully but aren’t delivered during the `5 minute` #deduplication interval. SQS remembers the `MessageDeduplicationId` values it’s seen for at least five minutes, which means deduplication Ids can only reduce, not completely eliminate, the chances of duplication occurring.
		- Optionally, you may enable `ContentBasedDeduplication` to let SQS create an SHA-256 hash based on the message body and use it as the value for `MessageDeduplicationId`.


## SQS Actions
- Batch Actions
	- Allows to manipulate up to `10 messages`.
	- Total size can not exceed `256 KİB`.
	- Can be used
		- Query API
		- AWS SDK
	- Actions
		- [SendMessageBatch](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessageBatch.html)
		* [DeleteMessageBatch](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_DeleteMessageBatch.html)
		- [ChangeMessageVisibilityBatch](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ChangeMessageVisibilityBatch.html)
- [SQS actions](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_Operations.html):
	- AddPermission
	- CancelMessageMoveTask
	- ChangeMessageVisibility
	- ChangeMessageVisibilityBatch
	- CreateQueue
	- [Delete Message](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_DeleteMessage.html)
	- DeleteMessageBatch
	- DeleteQueue
	- GetQueueAttributes
	- GetQueueUrl
	- ListDeadLetterSourceQueues
	- ListMessageMoveTasks
	- ListQueues
	- ListQueueTags
	- PurgeQueue
	- ReceiveMessage
	- RemovePermission
	- SendMessage
	- SendMessageBatch
	- SetQueueAttributes
	- StartMessageMoveTask
	- TagQueue
	- UntagQueue

