---
tags:
  - cloudformation
source: https://docs.aws.amazon.com/cloudformation/
---
## CloudFormation Features
- Templates can be
	- JSON
	- YAML
- Takes template from [[S3]]  and creates an evironment




## Concepts
### Stack [🔗](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks.html‬)
- Individual resources and treated as a single unit
- If it fails during creation, it reverts to its last working state if it has. Otherwise, all resources are terminated.
- `create-stack` command creates a stack as specified in the template provided.


## CloudFormation Template [🔗](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html)
- 🌗 **AWSTemplateFormatVersion** - lets you set the version of the template that you want to use, which determines the capabilities of the template. If you don’t assign a version, CloudFormation automatically assumes that you want to use the latest version.
- 🌗 **Description** - allows you to include comments about your template to help you better describe and add more details about your template.
- 🌗 **Metadata** - allows you to provide objects that provide details about the template or even implementation details of specific resources.
- 🌝 **Resources** - This is where you define all resources that you want to include in your stack.  AWS resources such as EC2 instance, Lambda function, S3 bucket are declared in the Resources section. Take note that the Resources section is the only REQUIRED section in a CloudFormation template. All other sections are optional.
- 🌗 **Mappings** - matches a key to a corresponding set of named values.
- 🌗 **Parameters** - when writing CloudFormation templates, it’s very unlikely to be hardcoding values such as AMI ID, security group ID, and so on. Parameters enable users to pass values dynamically based on their use case.
- 🌗 **Conditions** - defines conditions by using the intrinsic condition functions. These conditions determine when AWS CloudFormation creates the associated resources.
- 🌗 **Transform** - you can use this section to write templates in the AWS Serverless Application Model (AWS SAM) syntax. The syntax used in SAM is different from the standard CloudFormation template. Behind the scenes, CloudFormation converts and ‘transforms’ the templates written under the Transform section into a regular CloudFormation template.
- 🌗**Outputs** - Describes the values that are returned whenever you view your stack's properties. For example, you can declare an output for an S3 bucket name and then call the describe-stacks AWS CLI command to view the name.
	- Logical ID (also called logical name)
		- An identifier for the current output. The logical ID must be alphanumeric (a–z, A–Z, 0–9) and unique within the template.
	- Description (optional)
		- A String type that describes the output value. The value for the description declaration must be a literal string that's between 0 and 1024 bytes in length. You can't use a parameter or function to specify the description.
	- Value (required)
		- The value of the property returned by the aws cloudformation describe-stacks command. The value of an output can include literals, parameter references, pseudo-parameters, a mapping value, or intrinsic functions.
	- Export (optional)
		- The name of the resource output to be exported for a [cross-stack reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/walkthrough-crossstackref.html).

## Helper Scripts [🔗](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-helper-scripts-reference.html)
AWS CloudFormation provides the following Python helper scripts that you can use to install software and start services on an Amazon [[EC2]] instance that you create as part of your stack:
- Helper scripts don't require credentials
- Helper scripts
	- `cfn-init`
		- Use to retrieve and interpret resource metadata, install packages, create files, and start services.
	- `cfn-signal`
		- Use to signal with a CreationPolicy or WaitCondition, so you can synchronize other resources in the stack when the prerequisite resource or application is ready.
	- `cfn-get-metadata`
		- Use to retrieve metadata for a resource or path to a specific key.
	- `cfn-hup`
		- Use to check for updates to metadata and execute custom hooks when changes are detected.

>[!warning] 
>The scripts aren't executed by default. You must include calls in your template to execute specific helper scripts.



## CloudFormation Template Samples
### Mapping
```yaml
AWSTemplateFormatVersion: "2010-09-09"
Mappings: 
  RegionMap: 
    us-east-1:
      HVM64: ami-0ff8a91507f77f867
      HVMG2: ami-0a584ac55a7631c0c
    us-west-1:
      HVM64: ami-0bdb828fd58c52235
      HVMG2: ami-066ee5fd4a9ef77f1
    eu-west-1:
      HVM64: ami-047bb4163c506cd98
      HVMG2: ami-0a7c483d527806435
    ap-northeast-1:
      HVM64: ami-06cd52961ce9f0d85
      HVMG2: ami-053cdd503598e4a9d
    ap-southeast-1:
      HVM64: ami-08569b978cc4dfa10
      HVMG2: ami-0be9df32ae9f92309
Resources: 
  myEC2Instance: 
    Type: "AWS::EC2::Instance"
    Properties: 
      ImageId: !FindInMap [RegionMap, !Ref "AWS::Region", HVM64]
      InstanceType: m1.small
```


## Intrinsic Functions and Pseudo Parameters

### !Ref‬‭
Returns the value of a parameter or resource.‬‭ When you specify a resource's logical name, it‬‭ outputs a value that you can use to refer to that resource (e.g., instance id, bucket name, API ID).‬ ‭ Usually, in a stack, there’ll be dependencies between resources.
For example, if you want to attach an Elastic IP address (EIP) to an EC2 instance, you must specify the instance using its instance id. The thing is, the instance id won’t be available until the EC2 instance is created, so hardcoding it in the CloudFormation template is not possible. To get around this, we can use the Ref function to retrieve the instance id once it’s available and have CloudFormation insert it during stack creation.
```yaml
Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0cd490fb43e2559ed
      InstanceType: t2.micro
  MyEIP:
    Type: AWS::EC2::EIPz
    Properties:
        InstanceId: !Ref MyEC2Instance
```

### !FindInMap
This one only works with the Mappings section. FindInMap returns a named value based on a specified key. For example, suppose you have custom AMIs in us-east-1 and us-east-2. And you want to make sure their image ids are accessible at runtime regardless of the region the stack is deployed. First, create a two-level map under the Mappings section. Each map is a key that points to a name-value pair. Make the regions your top-level keys, followed by a name-value pair that represents the AMI name and its corresponding image id. Retrieve the image ids with FindInMap using the syntax below:
```yaml
Mappings:
  MyRegions:
    us-east-1:
      CustomUbuntu: ami-0cd490fb43e2559ed
    us-east-2:
      CustomUbuntu: ami-01581ffba5551cdf3

Resources:
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: !FindInMap [ MyRegions, !Ref "AWS::Region", CustomUbuntu ]
      InstanceType: t2.micro
```
 
### !GetAtt
Returns the value of an attribute from a resource. This intrinsic function works similar to how the !Ref function works. Except that this time, there are more values to choose from depending on the resource. 

Syntax for the function name (shorthand):

!GetAtt logicalNameOfResource.attributeName 

The AWS::EC2::Instance resource contains attributes such as the availability zone where the specified instance is launched, its private and public IP address, and public DNS name. If you want to retrieve the public IP of the MyEC2Instance resource, use the !GetAtt MyEC2Instance.PublicIp function. Always check the documentation for the attributes available to a particular resource.



- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks.html‬
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference.html‬
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html‬
