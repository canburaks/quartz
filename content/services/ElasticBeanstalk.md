---
tags:
  - "#elastic-beanstalk"
---
## ElasticBeanstalk Features
- Storage
	- EBS Volumes
		- Block storage that is assigned to EC2 instance.
		- Each volume can only be assigned to one instance.
- Instance Lifecycles
	- Pending
	- Terminated
	- Stopping
	- Stopped
	- Shutting-down
	- Terminated


### Confiturations
- Configurations can be saved:
	- ElasticBeanstalk Console
	- EB CLI
	- AWS CLI

### Configuration Changes [🔗](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environments-updating.html)
- Many configuration changes can be applied to a running environment without replacing existing instances. For example, setting a health check URL triggers an environment update to modify the load balancer settings.
- Changes that modify launch configuration or [[VPC]] settings require terminating all instances.
- Policies for configuration setting replacement:
	- 1️⃣ Rolling Updates: keeps minimum number of instances and serves traffic to them.
		- Rolling Based on Health (No single-instance environment)
		- Rolling Based on Time (No single-instance environment)
	- 2️⃣ Immutable Updates: ElasticBeanstalk launches a temporary Auto Scaling group outside of the environment with separate instances. After, those will be put behind load balancer. When new instances pass health checks, new instances are moved to environment's Auto Scaling group and terminates the temporary group and old instances.
	- 3️⃣ Disabled: ElasticBeanstalk terminates the existing environment's instances and replaces them with the new instances.

### Deployment Policies (`DeploymentPolicy`) [🔗](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.deploy-existing-version.html) 
- All at Once:
	- 🍏 Fastest 
	- 🍎 Involves a service interruption - downtime. 
	- Rolling back requires a further All at Once update.
- Rolling: (No single-instance environment)
	- Update a few instance at a time, then move onto the next bucket.
	- 🍎 Reduced capacity during deployment. 
	- 🍏 No additional cost.
	- Rolling back requires a further Rolling update.
- Rolling with Additional Batch: (No single-instance environment)
	- 🍏 Maintains full capacity.
	- 🍎 Extra additional cost.
	- 🍎 Longer deployment
	- Rolling back requires a further Rolling update.
- 🍃 Immutable:
	- Spins up new instances in a new ASG, deploys version to these instances and then swaps.
	- 🍏 Maintains full capacity.
	- 🍎 High cost (double capacity)
	- 🍎 Longest deployment
	- 🍃 Preferred for mission critical production systems.
- 🍃 Traffic Splitting: (No single-instance environment)
	- Performs an immutable deployment.
	- Splits the traffic between the old and the new deployment, enables #canary-testing

>[!info] Blue/Green Deployment
>ElasticBeanstalk also supports #blue-green-deployment . To do this, deploy the new version to a separate environment, and then swap the CNAMEs of the two environments to redirect traffic to the new version instantly.

### Config Files
 - `.ebextensions` folder: (Amazon Linux 1)
	 - files must have `.config` extension.
 - Procfile
 - Buildfile
 - Platform Hooks



### Rolling Updates [🔗](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html)
``.ebextensions/rolling-updates.config``
```config
option_settings:
  aws:elasticbeanstalk:command:
    DeploymentPolicy: Rolling
    BatchSizeType: Percentage
    BatchSize: 25
```