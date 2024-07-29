---
tags:
  - api-gateway
source: https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html
---
 

They are not used in API Gateway to determine which version of the API to route traffic to based on the request header.

## API Gateway Features
- Fully managed service for RESTful APIs.
- Back-end service that are powered by
	- [[Lambda|AWS Lambda]]
	- [[EC2|AWS EC2]]
	- [[ElasticBeanstalk|AWS ElasticBeanstalk]]
	- [[ECS|AWS Elastic Container Service (ECS)]]
	- Any web application
- API Gateway’s integration #timeout is `29 seconds`
- API Gateway Stage
	- API Gateway Stage is a named reference to a deployment (a snapshot of the APl).
	- Use a stage to manage and optimize a particular deployment.
	- You can configure stage settings to enable caching, customize request throttling, configure logging, define stage variables, or attach a canary release for testing.
- Environment Variables
	- Used to pass variables and parameters to a [[Lambda]] function. 


>[!warning]
>API Gateway is used to expose APls and route API requests to their correct destination. It is not used to build and manage complex business workflows for distributed systems. Otherwise, use [[Step Functions]].


## Request & Response Flow
- A simple request-response flow
	- 1️⃣ Method Request
		- Defines #http-header
		- Client request is validated
		- Authoriation can be set up in here
		- Validate `body` for a [[Lambda]] function.
		- Declare any input `body` such as #query-string
	- 2️⃣ Integration Request
		- The section contains settings about 
			- How API Gateway communicates with the backend
				- Lamda function
				- HTTP endpoint
				- Mock
				- AWS Service
				- VPC Link
			- Integration type
				- Proxy
					- Client request is transmitted as-is to the backend
				- Non-proxy
					- Option to use mapping templates to model the structure of the request daya that gets forwarded to the backend.
					- Change data
	- 3️⃣🌝 Integration Response (Only in Non-proxy)
		- Intercepts the result before it is returned to the client
		- Must configure at least one Integration Response
			- Default: `Passthrough` which returns the result as-is
		- Can transform the response to another format like #base64 
	- 4️⃣ Method Response
		- Defines #http-header the method can return

## API Types
- REST API
	- Complete control on request-response flow
		- Create API Keys
		- Caching
		- UsagePlans
- HTTP API
	- Cheaper than REST API
	- Use for simple applications
	- Lacks many API Gateway features
- WebSocket API
	- Use for real-time applications
- SOAP (Support)
	- Passthrough
	- Convert XML to JSON

## Methods
- Non-proxy
	- Lambda function
	- HTTP
	- Mock
	- AWS Service
	- VPC Link
- Proxy
	- Lamda Proxy
	- HTTP Proxy

https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-vs-rest.html
https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-basic-concept.html

https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-integration-types.html

---

## Stage Variables
Allows to use different Lambda versions by using stage variables as a placeholder.

https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html
https://docs.aws.amazon.com/apigateway/latest/developerguide/canary-release.html

---

## Caching
- API Gateway doesn't update the cache entries until #ttl expires
- Invalidate by using `'Cache-Control': 'max-age=0'` 
- In order to [invalidate cache](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html#invalidate-method-caching) by client
	- `Cache-Control: max-age=0` #http-header must be sent.
	- `"execute-api: InvalidateCache"` [[IAM]] #role must be granted. 

‭

## Usage Plans
https://aws.amazon.com/blogs/aws/new-usage-plans-for-amazon-api-gateway/
https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-usage-plans.html



