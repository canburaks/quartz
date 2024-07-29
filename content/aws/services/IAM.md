---
tags:
  - permission
  - role
  - policy
source: https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html
---
## IAM Features
- Identity Sources
	- Identity Center directory
	- Active Directory
	- External identity provider


## IAM Identity
can be an:
- IAM User
- IAM Role
- IAM Group

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups.html
https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html
https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html


### IAM Roles
- An IAM role can be assumed by AWS Services to perform actions on your behalf.
- IAM Roles are for:
	- AWS Services
	- External Users (IdP)
- IAM Roles use time-limited security credentials.

### IAM Groups
- The policies attached to an IAM Group are inherited by the IAM users under it.
- It’s possible to assign an IAM user to multiple groups.
- Groups cannot be nested.

## IAM Policy 
- An IAM identity cannot perform AWS actions without an IAM Policy attached to it unless the resource being accessed allows the IAM Identity to do so.
- AWS evaluates all explicit `DENY` requests first.
- Resource-based policies are evaluated first.

https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html‬


### IAM Policy Types to Choose
- AWS Managed Policies
- Customer-managed Policy
- Inline Policy
	- When you use an inline policy, the permissions in the policy cannot be inadvertently attached to the wrong principal entity.
	- Doesn't have its own ARN
	- Can't be referenced

### Types of IAM Policies
- Identity-based policies
- Resource-based policies
- IAM permissions boundaries
- Service Control Policies
- Session policies
- Access Control Lists (ACLs)

### IAM Policy Structure
It is made up of two components:
- Policy-wide information 🌗
	- Version element
- Statement(s)
	- 🌗 Statement ID (Sid) 
		- Label purpose - useful for identification
	- 🌝 Effect
		- Can be only `Allow` or `Deny`
	- 🌝 Action
		- List of actions
		- Use `*` grant access to all or subset of AWS operations:
			-  `ec2:DescribeInstance*` grants also
				- `DescribeInstanceAttribute`, 
				- `DescribeInstances` 
				- `DescribeInstancesStatus`
	- 🌝 Resource (Identity-based)
		- List of AWS resources to which the `Action` element is applied.
		- Use #arn if you need to be more restrictive.
		- Use `*` to apply the `Action` to all resources. 
	- 🌝 Principal (Resource-based) [🔗](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html)
		- Specifies whom can access it
	- 🌗 Condition
		- Useful for applying logic to a policy.
		- Some conditions
			- StringEquals
			- StringNotEquals
			- StringLike
			- StringNotLike
			- Bool
			- IpAddress
			- NotIpAddress
			- ArnEquals
			- ArnNotEquals
			- Null: check if key is present
		- We can add `IfExist` to any condition above (except `Null` condition)
			- StringLikeIfExists

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-policy-structure.html
https://aws.amazon.com/blogs/security/back-to-school-understanding-the-iam-policy-grammar/

### Identity-based Policy vs. Resource-based Policy
- Identity-based Policies
	- Defines what an `IAM Identity` can do
	- Can be attached to an IAM Identity
		- IAM User
		- IAM Group
		- IAM Role
- Resource-based policies
	- Defines who can access that resource
	- Can be attached to AWS resources
		- [[S3]] (bucket policy)
		- [[KMS]] (key policy)
		- [[Lambda]] function

https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_identity-vs-resource.html‬

### Policy Examples
#### Identity-based Policy
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowFullEC2AccessFromMyNetwork",
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInstance*"
            ],
            "Resource": "*",
            "Condition": {
                "IpAddress": {
                    "aws:SourceIp": "180.0.111.0/24"
                }
            }
        }
    ]
}
```

#### Resource-based Policy
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::123456789000:user/john"
            },
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": [
                "arn:aws:s3:::tdojo/john-folder"
            ]
        },
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::123456789000:user/dave"
            },
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],

"Res
```

### Cross-account Access
If a user `U` from account `A` needs to access resource `X` from account `B`:
- Account `B` must attach a resource-based policy to `X` that defines user `X` as a `Principal`.
- Account `A` must attach an Identity-based policy to user `U` that defines `X` as a `Resource`

## IAM Roles
### IAM:PassRole
PassRole is a special type of IAM permission that permits a user to associate IAM roles to an AWS resource. This is a simple yet powerful permission that warrants due scrutiny when constructing IAM Policies. 
#### Scenario
For example:
- User `U` has permissions to access resource `A`, but not `B`.
- Resource `A` has permissions to execute on resource `B`.
If we:
- Attach `iam:PassRole` action to user `U`
Then:
- User `U` can access to resource `B`.
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "*"
        }
    ]
}
```

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html‬
https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_iam-passrole-service.html‬


### Role-Based Access Control
This method simplifies the management of access controls by grouping permissions based on roles rather than managing permissions for each individual user.
- Can be allocated to anyone including groups or services.
https://docs.aws.amazon.com/prescriptive-guidance/latest/saas-multitenant-api-access-authorization/access-control-types.html


‭