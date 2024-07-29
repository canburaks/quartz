---
tags:
  - code-deploy
  - ci-cd
source: https://docs.aws.amazon.com/codedeploy/
---


## CodeDeploy Features
- Uses Port `443`
- Lifecycle
	- ApplicationStop
	- BeforeInstall
	- AfterInstall
	- ApplicationStart
	- ValidateService
- Compute platforms available:
	- [[EC2]] / On-premises
	- [[ECS]]
	- [[Lambda]]
- Deployment configurations
	- Canary
		- Traffic is shifted in two increments.
	- Linear
		- Traffic is shifted in equal increments with an equal number of minutes between each increment.
	- All-at-once
		- All traffic is shifted.
- Deployment types
	- In-place
		- Only deployments that use the EC2/On-Premises compute platform can use in-place deployments.
	- Blue/Green


## AppSpec File
### AppSpec File Structure
#### ECS 
```yaml
version: 0.0
resources: 
  ecs-service-specifications
hooks: 
  deployment-lifecycle-event-mappings
```

#### EC2
```yaml
version: 0.0
os: operating-system-name
files:
  source-destination-files-mappings
permissions:
  permissions-specifications
hooks:
  deployment-lifecycle-event-mappings
```

#### Lambda
```yaml
version: 0.0
resources: 
  lambda-function-specifications
hooks: 
  deployment-lifecycle-event-mappings
```

### Example AppSpec File for EC2
```yaml
version: 0.0
os: linux
files:
  - source: /
    destination: /var/www/html/WordPress
hooks:
  BeforeInstall:
    - location: scripts/install_dependencies.sh
      timeout: 300
      runas: root
  AfterInstall:
    - location: scripts/change_permissions.sh
      timeout: 300
      runas: root
  ApplicationStart:
    - location: scripts/start_server.sh
    - location: scripts/create_test_db.sh
      timeout: 300
      runas: root
  ApplicationStop:
    - location: scripts/stop_server.sh
      timeout: 300
      runas: root

```