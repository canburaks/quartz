---
aliases:
  - Lambda
tags:
  - lamda
source: https://docs.aws.amazon.com/lambda/latest/dg/welcome.html
---
## Lambda Features
- Memory 
	- It scales proportinally to the CPU power.
	- It can range from `128 MB` to `10240 MB` in `1 MB` increments.
- Timeout
	- The default #timeout is `3 seconds`
	- Maximum #timeout value of `900 seconds`
- Invocation ways
	- AWS Lambda Console
	- API/CLI command
	- The URL of Lambda function
		- Auth types
			- NONE
			- AWS [[IAM]]
- Triggered by (AWS Services)
	- DynamoDB
	- Kinesis
	- SQS
	- Application Load Balancer
	- API Gateway
	- Alexa
	- CloudFront
	- S3
	- SNS
	- SES
	- CloudFormation
	- CloudWatch
	- CodeCommit
	- CodePipeline
- Services that don't invoke lambda functions
	- Stream or queue-based resources
		- DynamoDB streams
		- SQS queues
		- Kinesis Data Streams‬
- Supported languages
	- Node.js
	- Java
	- Python
	- C#
	- Go
- Execution environment lifecycles
	- `INIT`
	- `INVOKE`
	- `SHUTDOWN`


## Invocation types
- Synchronous Invocation
	- Can accept a payload of up to `6 MB`
	- Need a retry logic to handle intermittent errors.
	- Services that invoke Lambda functions synchronously
		- [[API Gateway]]
		- Application Load Balancer
		- [[Cognito]]
		- Data Firehose
		- Cloudfront (Lambda@Edge)
- Asynchronous Invocation
	- Can accept to a payload of up to `256 KB`
	- AWS stores the event in an internal queue
	- Use cases
		- Long-latency processes that run in the background
		- Batch Operations
		- Video Encoding
		- Order processing
	- Services that invoke Lambda functions asynchronously
		- [[API Gateway]] by specifying `Event` in the  `X-AMZ-Invocation-Type` request header.
		- [[S3]]
		- [[CloudWatch]]
		- EventBridge
		- [[CodeCommit]]
		- [[CloudFormation]]
		- AWS Config


## Destinations
- Destination Options
	- Source
		- Asynchronous invocation
		- Event source mapping invocation
	- Condition
		- On failure
		- On success
	- Destination type
		- SNS topic
		- [[SQS|SQS Queue]]
		- Lambda function
		- EventBridge event bus
	- Destination
		- #arn 


### Example CLI Invocation
#### Synchronous Invocation
```shell
aws lambda invoke \
    --function-name testFunction \
    --invocation-type RequestResponse \
    --cli-binary-format raw-in-base64-out \
    --payload '{"input": "input_value"}' response.json
```

#### Asynchronous Invocation
```shell
 aws lambda invoke \
--function-name testFunction \
--invocation-type Event \
--cli-binary-format raw-in-base64-out \
--payload '{"input":"input_value"}' response.json

```

‬
https://docs.aws.amazon.com/lambda/latest/dg/lambda-invocation.html‭ 
https://aws.amazon.com/blogs/architecture/understanding-the-different-ways-to-invoke-lambda-functions/‬


### Event source mappings
An event source mapping is a sort of polling agent that Lambda manages.
- Invokes a lambda function synchronously if:
	- The batch size is reached
	- The maximum batching window is reached

---
## Deployment
- You can upload a zip file directly to the console
- You can upload a zip file to [[S3]], and Lambda can download it from there
- You can paste code directly to the console


## Deploying Code with External Dependencies
- 🍂 Zip the local code and deploy to AWS Lambda
	- Deploying multiple Lambda functions that have the same dependencies is inefficient
	- Steps
		- Install all external dependencies
		- Create a deployment package in `zip` format.
		- Upload to AWS Lambda
- 🍃 Lambda Layers
	- Lambda Layers lets you store any additional code separately from your deployment package
	- Lambda layers can be shared among Lambda functions
	- Like a deployment package, external dependencies must be in zip format before being uploaded to AWS Lambda


https://docs.aws.amazon.com/serverlessrepo/latest/devguide/sharing-lambda-layers.html
https://aws.amazon.com/blogs/aws/new-for-aws-lambda-use-any-programming-language-and-share-common-components

---

## Concurrency and Throttling
- `1000 unreserved` concurrent executions across all Lambda functions for every region per account. (Can be increased by contacting AWS Support)
- When distributing concurrencies, `unreserved count` can not be less than `100`. In other words, `reserved concurrent execution` is limited to `900`.  
- Returns `429` #http-status-code
	- A function reaches its `reserved conccurrency` limit
	- A function reaches `unreserved concurrency` limit of the account  
	- When computing #concurrency limit for a Lambda function consider:
		- Execution time
		- Number of requests handled per second
		- Calculations differ for Poll-based event sources and push-based

### Example Scenario
Lambda function
- processes HTTP requests from API Gateway with unreserved concurrency
- Expects an average execution time of 10 seconds
- Expects 150 requests per second.
At is can be seen from below, after 6th second, many requests will sart to fail.

| Second    | 1   | 2   | 3   | 4   | 5   | 6   | 7    | 8    | 9    | 10   |
| --------- | --- | --- | --- | --- | --- | --- | ---- | ---- | ---- | ---- |
| Request   | 150 | 150 | 150 | 150 | 150 | 150 | 150  | 150  | 150  | 10   |
| Functions | 150 | 300 | 450 | 600 | 750 | 900 | 1050 | 1200 | 1350 | 1500 |

https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html
https://aws.amazon.com/blogs/compute/managing-aws-lambda-function-concurrency/

---

## Connections to a VPC
- Lambda functions live in an isolated and secured VPC managed by AWS
- Lambda function can interact with resources inside a private subnet by
	- Should have `AWSLambdaVPCAccessExecutionRole` permission (creation and deletion of `Elastic Network Interface`)
- Even if Lambda function is connected to public subnet, #vpc still be unable to route traffic between Lambda function and the internet. To fix this:
	- Create a `NAT Gateway` in `public subnet` in the `VPC`
	- Add entry to `private subnet`'s `route table`.
		- Destination: `0.0.0.0/0`
		- Target: `NAT Gateway` 


https://aws.amazon.com/premiumsupport/knowledge-center/internet-access-lambda-function

https://docs.aws.amazon.com/lambda/latest/dg/configuration-vpc.html

---

## Version and Aliases [🔗](https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html)
- AWS Lambda assigns version numbers monotonically which means version number always increases even if it is deleted.
- Unpublished version is referred as `$LATEST`.
- Function uses `$LATEST` if the version is not specified.
- When you publish a new version, a `:version-number` suffix is appended to your function’s #arn, which indicates its version, (`1`)  
	- `arn:aws:lambda:us-east-2:123456789123:function:cool-function‬‭:1‬`
- Invoke a function with an #alias is possible.
	- Append `:alias-name` suffix to the function #arn
	- Every #alias has its own unique #arn 

https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html
https://docs.aws.amazon.com/lambda/latest/dg/configuration-versions.html

---
 
## Lambda Layers
### The paths for different runtimes of #lambda-layers

| Runtime      | Path                                                  |
| ------------ | ----------------------------------------------------- |
| Node.js      | nodejs/node_modules                                   |
|              | nodejs/node16/node_modules (`NODE_PATH`)              |
|              | nodejs/node18/node_modules (`NODE_PATH`)              |
|              | nodejs/node20/node_modules (`NODE_PATH`)              |
| Python       | python                                                |
|              | python/lib/python3.x/site-packages (site directories) |
| Java         | java/lib (CLASSPATH)                                  |
| Ruby         | ruby/gems/3.2.0 (GEM_PATH)                            |
|              | ruby/lib (RUBYLIB)                                    |
| All runtimes | bin (PATH)                                            |
|              | lib (LD_LIBRARY_PATH)                                 |
https://docs.aws.amazon.com/lambda/latest/dg/packaging-layers.html#packaging-layers-paths
