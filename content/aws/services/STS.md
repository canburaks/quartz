---
tags:
  - sts
---
## STS Features
- API
	- `DecodeAuthorizationMessage` 
		- Decodes additional information about the authorization status of a request from an encoded message returned in response to an AWS request.
		- If a user is not authorized to perform an operation that he or she has requested, the request returns a `Client.UnauthorizedOperation` response (a `403` #http-status-code  response). 
		- Some AWS operations additionally return an encoded message that can provide details about this authorization failure.
		- The message is encoded because the details of the authorization status can constitute privileged information that the user who requested the operation should not see. To decode an authorization status message, a user must be granted permissions via an [[IAM]] policy to request which must include `sts:DecodeAuthorizationMessage` action. 