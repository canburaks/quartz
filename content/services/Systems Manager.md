---
tags:
  - systems-manager
---

## Application Management
### Parameter Store
- Parameter policies (Only Advanced tier):
	- `Expiration`
		- Deletes the parameter at a specific date
	- `ExpirationNotification`
		- Sends an event to [[EventBridge|Amazon EventBridge]] (Amazon CloudWatch Events) when the specified expiration time is reached.
	- `NoChangeNotification`
		- Sends an event to Amazon EventBridge (Amazon CloudWatch Events) when a parameter has not been modified for a specified period of time.
- Can store
	- Plain text
	- Encrypted text
- Referencing
	- `{{ssm:parameter-name}}`
	- No nested values

### Node Management
- Automates the process of keeping your managed nodes and other services.
- Schedules the association checks.
- An association includes three components: 
	- A document that defines the state
	- Target(s)
	- A schedule