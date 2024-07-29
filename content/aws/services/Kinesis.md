---
tags:
  - kinesis
  - stream
---

## Kinesis Features
- Number of consumer instances does not exceed the number of shards.
- Amazon Kinesis Data Streams limits of `5 GetRecords` calls per second.
- 🍃 We recommend polling each shard one time per second per application. 
- Monitoring
	- The metrics collected for streams are pushed to [[CloudWatch]] `every minute`. Archived `2 weeks`. 
	- Monitoring types:
		- Basic (stream-level)
			- Stream-level data is sent automatically every minute at no charge.
		- Enhanced (shard-level)
			- Shard-level data is sent every minute for an additional cost. Must be specifically enabled.



A stream is composed of one or more shards, each of which provides a fixed unit of capacity. The total capacity of the stream is the sum of the capacities of its shards. The Kinesis Client Library (KCL) ensures that for every shard there is a record processor running and processing that shard.

### Custom Consumers with Dedicated Throughput (Enhanced Fan-Out) [🔗](https://docs.aws.amazon.com/streams/latest/dev/enhanced-consumers.html)
- Enables consumers to receive records from a stream with throughput of up to `2 MB` of data per second per shard.
- Kinesis Data Streams pushes data records from the stream to consumers that use enhanced fan-out.  (don't need to poll for data)

## Handling Duplication
- Reasons for delivering multiple times:
	- Producer retries
	- Consumer retries
- Handling duplication
	- The number of duplicates due to producer retries is usually low compared to the number of duplicates due to consumer retries.
	- Producer retries
		- Embed primary key within the record to remove duplicates.
	- Consumer retries
		- Record Processor uses a fixed number of records per Amazon S3 file, such as 5000.
		- The file name uses this schema: [[S3|Amazon S3]] prefix, `shard-id`, and `First-Sequence-Num`. In this case, it could be something like `sample-shard000001-10001`
		- After you upload the Amazon S3 file, checkpoint by specifying `Last-Sequence-Num`. In this case, you would checkpoint at record number 15000.











