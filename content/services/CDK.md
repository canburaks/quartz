---
tags:
  - cdk
source: https://docs.aws.amazon.com/cdk/v2/guide/home.html
---
## Cloud Development Kit
- The AWS CDK is an open-source framework that you can use to manage your AWS infrastructure using code.
- CDK consists of two parts:
	- AWS CDK Contruction Library [🔗](https://docs.aws.amazon.com/cdk/v2/guide/constructs.html)
	- AWS CDK Toolkit  [🔗](https://docs.aws.amazon.com/cdk/v2/guide/cli.html)
- Files and folders:
	- `cdk.json`
		- Configuration file for the AWS CDK. 
		- This file provides instruction to the AWS CDK CLI regarding how to run your app.
- CDK available in those languages:
	- TypeScript
	- JavaScript
	- Python
	- Java
	- C#
	- Go

## AWS CDK Concepts
- Apps
	- A collection of one or more CDK stacks.
	- Doesn't configure AWS resources on their own. 
	- Provide context for other constructs.
- Stacks
	- A collection of one or more constructs.
	- Doesn't configure AWS resources on their own.
	- Provide context for other constructs.
- Node [🔗](https://docs.aws.amazon.com/cdk/api/v2/docs/constructs.Node.html)




