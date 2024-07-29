---
tags:
  - cloudwatch
---

## CloudWatch Features
- CloudWatch alarms can be used to send [[SNS]] notifications when an alarm changes state.
- CloudWatch is not the correct service to use to send a copy of the  invocation record for failed [[Lambda]] invocations.

## CloudWatch Concepts
- Namespaces
	- A namespace is a container for CloudWatch metrics. Metrics in different namespaces are isolated from each other, so that metrics from different applications are not mistakenly aggregated into the same statistics.
	- The AWS namespaces typically use the following naming convention: `AWS/{service}`service. For example, Amazon EC2 uses the `AWS/EC2` namespace.
- Metrics
	- Time-ordered set of data points that are published to CloudWatch.
	- Metrics cannot be deleted, but they automatically expire after `15 months` if no new data is published to them.
	- Data points older than `15 months` expire on a rolling basis
	- Metrics are uniquely defined by a name, a namespace, and zero or more dimensions.
	- Each data point in a metric has a time stamp, and (optionally) a unit of measure.
- Dimensions
	- A dimension is a name/value pair that is part of the identity of a metric.
	- You can assign up to `30 dimensions` to a metric.


## Metrics
- Retention
	- Data points less than `60 seconds` are available for `3 hors`. (High-res)
	- Data points with a period of 
		- `60 seconds` are available for `15 days`.
		- `300 seconds` are available for `63 days`.
		- `3600 seconds` are available for `455 days` / `15 months`.
- Concepts
	- Count
		- Count relates to the total number of API requests in a given period. It will not help you to understand why the API is timing out.
	- Latency
		- Latency relates to the time between when [[API Gateway]] receives a request from a client and when it returns a response to the client. The latency includes the integration latency and other [[API Gateway]] overhead. This is useful because a latency issue could be causing the #timeout.
	- IntegrationLatency
		- IntegrationLatency relates to the time between when [[API Gateway]] relays a request to the backend and when it receives a response from the backend. This is useful because a latency issue could be causing the timeout.


>[!warning]  Memory Utilization
>The metrics that AWS provides can be collected at the hypervisor level. But memory metrics (like disk metrics) is from the OS level. So it is a custom metric that you have to periodically push to CloudWatch. [Sourde](https://stackoverflow.com/questions/44507609/why-aws-cloudwatch-does-not-have-memory-usage-metric-for-autoscaling-group)
