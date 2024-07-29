---
tags:
  - ecs
  - container
---
## ECS Features
- ECS Instance
	- Instances are deregistered
		- Manually if the instance in the `STOPPED` state.
		- Automatically if the instance is the `RUNNING` state.
- ECS Roles
	- Container instance role
		- Allows ECS container agent running in instances to call ECS API on our behalf. It attaches ` ecsInstanceRole` #role
	- Task execution role [🔗](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html)‬
	- Task role [🔗](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html)
- Tasks
	- Parameters which are defined in a task:
		- Docker image to use with each container in a task.
		- CPU and memory allocation
		- Launch type to use
			- EC2
			- Fargate
		- Logging configuration
			- bridge
			- host
			- awsvpc
			- none
		- Volumes that should be mounted
		- Task execution #iam role
- Task placement strategies [🔗](https://aws.amazon.com/blogs/compute/amazon-ecs-task-placement/)
	- **binpack**
		- Places tasks on a few instaces as possible.
		- Place tasks based on the least available amount of CPU or memory. This minimizes the number of instances in use.
	- **random**
		- Place tasks randomly.
		- No config is required
	- **spread**
		- Maximize availability
		- Place tasks evenly based on the specified value. 
		- Accepted values are attribute key-value pairs, _instanceId_, or _host_.


## Sample Task Definitions
### Fargate
```json
{
    "family": "sample-fargate", 
    "networkMode": "awsvpc", 
    "containerDefinitions": [
        {
            "name": "fargate-app", 
            "image": "public.ecr.aws/docker/library/httpd:latest", 
            "portMappings": [
                {
                    "containerPort": 80, 
                    "hostPort": 80, 
                    "protocol": "tcp"
                }
            ], 
            "essential": true, 
            "entryPoint": [
                "sh",
		"-c"
            ], 
            "command": [
                "/bin/sh -c \"echo '<html> <head> <title>Amazon ECS Sample App</title> <style>body {margin-top: 40px; background-color: #333;} </style> </head><body> <div style=color:white;text-align:center> <h1>Amazon ECS Sample App</h1> <h2>Congratulations!</h2> <p>Your application is now running on a container in Amazon ECS.</p> </div></body></html>' >  /usr/local/apache2/htdocs/index.html && httpd-foreground\""
            ]
        }
    ], 
    "requiresCompatibilities": [
        "FARGATE"
    ], 
    "cpu": "256", 
    "memory": "512"
}
```