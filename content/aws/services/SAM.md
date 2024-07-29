---
tags:
  - sam
  - serverless
---
## AWS SAM Features
- An extension of [[CloudFormation]] template.
- CLI commands: #cli
	- **`sam init`**
		- Initializes a serverless application with an AWS SAM template. 
		- The template provides a folder structure for your [[Lambda]] functions
		- The template is connected to an event source such as 
			- APIs
			- [[S3]] buckets
			- [[DynamoDB]] tables.
		- This application includes everything you need to get started
		- Eventually extend it into a production-scale application.
	- **`sam build`
		- Builds any dependencies that your application has
		- Copies your application source code to folders under `.aws-sam/build` to be zipped and uploaded to Lambda.
	- **`sam deploy`**
		- Performs the functionality of `sam package`. 
		- You can use the `sam deploy` command to directly package and deploy your application.

