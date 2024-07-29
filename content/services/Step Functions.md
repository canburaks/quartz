---
tags:
  - step-functions
  - workflow
  - orchestration
source: https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html
---
## Step Function Concepts [🔗](https://docs.aws.amazon.com/step-functions/latest/dg/getting-started-with-sfn.html#key-concepts-get-started)
- Workflow
	- A sequence of steps
- States [🔗](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-states.html)
	- Individual steps in your state machine that can make decisions based on the input
	- Perform actions from those inputs, and pass output to other states.
	- States can perform a variety of functions in your state machine:
		*   Do some work in your state machine (a [Task](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-task-state.html) state)
		*   Make a choice between branches of execution (a [Choice](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-choice-state.html) state)
		*   Stop an execution with a failure or success (a [Fail](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-fail-state.html) or [Succeed](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-succeed-state.html) state)
		*   Pass its input to its output, or inject some fixed data into the workflow (a [Pass](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-pass-state.html) state)
		*   Provide a delay for a certain amount of time, or until a specified date and time (a [Wait](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-wait-state.html) state)
		*   Begin parallel branches of execution (a [Parallel](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-parallel-state.html) state)
		*   Dynamically iterate steps (a [Map](https://docs.aws.amazon.com/step-functions/latest/dg/amazon-states-language-map-state.html) state)
* Actions [🔗](https://docs.aws.amazon.com/step-functions/latest/apireference/API_Operations.html)



## Step Functions Features
- Types of #workflow s
	- Standard workflow
		- Tasks and states are never run more than once, unless you have specified `Retry` behavior in ASL.
		- 💰 Billed according to the number of state transitions processed.
		- Up to `1 year`
		- Durable and auditable.
		- Rerieve execution history up to `90 days` with its [API](https://docs.aws.amazon.com/step-functions/latest/apireference).
	- Express
		- Up to `5 minutes`
		- Ideal for high-volume and event-processing workloads such as 
			- IoT data ingestion
			- Streaming and tranformation of data processing
		- 💰 Billed by 
			- The number of executions
			- The duration of execution
			- The memory consumed while the execution ran
- Can be used to build 
	- Distributed applications
	- Automate processes
	- Orchestrate microservices 
- Ability to 
	- Coordinate system activities
	- Run business logic in a particular sequence
	- Handle reprocessing of data if errors occur
	- 
- Provides a visual interface to the system, making it easy to update and change the workflow.
- Very low maintenance overhead.

## Error Handling [🔗](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-error-handling.html)
- When a state reports an error and either there is no `Retry` field, or if retries fail to resolve the error, Step Functions scans through the `catchers` in the order listed in the array. 
- When the error name appears in the value of a `catcher`’s `ErrorEquals` field, the state machine transitions to the state named in the `Next` field.
- `Retry`: 
	- Task and Parallel states can have a field named `Retry`, whose value must be an array of objects known as `retriers`.
	- An individual retrier represents a certain number of retries, usually at increasing time intervals.
	- A `retrier` contains the following fields:
		- `ErrorEquals`
			- A non-empty array of strings that match error names. When a state reports an error, Step Functions scans through the retriers. When the error name appears in this array, it implements the retry policy described in this retrier.
		- `IntervalSeconds`
			- An integer that represents the number of seconds before the first retry attempt.
			- 📌 (1 by default).
		- `MaxAttempts`
			- A positive integer that represents the maximum number of retry attempts 
			    - 📌 (3 by default). 
			- A value of `0` specifies that the error or errors are never retried.
			- If the error recurs more times than specified, retries cease and normal error handling resumes. 
		- `BackoffRate`
			- The multiplier by which the retry interval increases during each attempt 
				- 📌 (2.0 by default)
- `Catch`: 
	- Task and Parallel states can have a field named `Catch`. 
	- This field’s value must be an array of objects, known as `catchers`.
	- A catcher contains the following fields.
		- `ErrorEquals`
		- A non-empty array of strings that match error names, specified exactly as they are with the retrier field of the same name.
		- `Next`
		- A string that must exactly match one of the state machine’s state names.
		- `ResultPath`
		- A path that determines what input is sent to the state specified in the Next field.
			






## State Machine Structure
- 🌗`Comment`
	- A human-readable description of the state machine.
- 🌝`StartAt`
	- A string that must exactly match (is case sensitive) the name of one of the state objects.
- 🌗`TimeoutSeconds`
	- The maximum number of seconds an execution of the state machine can run. If it runs longer than the specified time, the execution fails with a `States.Timeout` [Error Name](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-error-handling.html#error-handling-error-representation).
- 🌗 `Version`
	- The version of the Amazon States Language used in the state machine (default is "1.0").
- 🌝`States` 
	- An object containing a comma-delimited set of states.


## Example State Language Specifications
```json
{
  "Comment": "An example of the Amazon States Language using a choice state.",
  "StartAt": "FirstState",
  "States": {
    "FirstState": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123456789012:function:FUNCTION_NAME",
      "Next": "ChoiceState"
    },
    "ChoiceState": {
      "Type" : "Choice",
      "Choices": [
        {
          "Variable": "$.foo",
          "NumericEquals": 1,
          "Next": "FirstMatchState"
        },
        {
          "Variable": "$.foo",
          "NumericEquals": 2,
          "Next": "SecondMatchState"
        }
      ],
      "Default": "DefaultState"
    },

    "FirstMatchState": {
      "Type" : "Task",
      "Resource": "arn:aws:lambda:us-east-1:123456789012:function:OnFirstMatch",
      "Next": "NextState"
    },

    "SecondMatchState": {
      "Type" : "Task",
      "Resource": "arn:aws:lambda:us-east-1:123456789012:function:OnSecondMatch",
      "Next": "NextState"
    },

    "DefaultState": {
      "Type": "Fail",
      "Error": "DefaultStateError",
      "Cause": "No Matches!"
    },

    "NextState": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123456789012:function:FUNCTION_NAME",
      "End": true
    }
  }
}
```


