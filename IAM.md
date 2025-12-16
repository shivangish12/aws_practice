IAM users, Root Users
Root user is required only for:
- Account-level settings
- Billing
- Closing the account
## 1. IAM Identities
### IAM Users
- Represents a person or application
- Has long-term credentials
- Credentials:
  - Console password
  - Access keys (CLI/API)
- Has no permissions by default

### IAM Roles
- Intended to be assumed
- Has no long-term credentials
- Provides temporary security credentials
- Common use cases:
  - AWS services (EC2, Lambda)
  - Cross-account access
  - Federated users

### IAM Groups
- Collection of IAM users
- Used to simplify permission management
- Groups:
  - Can contain users only
  - Cannot be nested
  - Are not principals

## 2. Principals
A principal is an entity that makes a request to AWS.

Types of principals:
- IAM user
- IAM role
- AWS service
- Federated identity

Only users and roles can be principals.


## 3. IAM Resource
The IAM service stores these resources. You can add, edit, and remove them from the Console.
IAM user
IAM group
IAM role
Permission policy
Identity-provider object

## 4. Components of a request
When a principal tries to use the AWS Management Console, the AWS API, or the AWS CLI, that principal sends a request to AWS. The request includes the following information:
- Actions or operations – The actions or operations that the principal wants to perform., such as an action in the AWS Management Console, or an operation in the AWS CLI or AWS API.
- Resources – The AWS resource object upon which the principal requests to perform an action or operation.
- Principal – The person or application that used an entity (user or role) to send the request. Information about the principal includes the permission policies.
- Environment data – Information about the IP address, user agent, SSL enabled status, and the timestamp.
- Resource data – Data related to the resource requested, such as a DynamoDB table name or a tag on an Amazon EC2 instance.



IAM Entity
IAM resources that AWS uses for authentication. Specify the entity as a Principal in a resource-based policy.
IAM user
IAM role

Policy>Group>Entity>Principle

Policies and Permissions-

• Users or Groups can be assigned JSON documents called policies
• These policies define the permissions of the users
• In AWS you apply the least privilege principle
Trust policy – Defines which principal can assume the role, and under which conditions. A trust policy is a specific type of resource-based policy for IAM roles. A role can have only one trust policy.
Identity-based policies (inline and managed) – These policies define the permissions that the user of the role is able to perform (or is denied from performing), and on which resources.

## 5. Policies
Policies are JSON documents that define permissions.

They specify:
- Which actions are allowed or denied
- On which resources
- Under what conditions

### Policy Structure

{
  "Effect": "Allow | Deny",
  "Action": "service:operation",
  "Resource": "arn"
}


## 6. Types of Policies
### Identity-Based Policies

Attached to:
-Users
-Groups
-Roles

Types:
-AWS managed
-Customer managed
-Inline

### Resource-Based Policies

-Attached directly to resources
-Include the Principal

Examples:
- S3 bucket policy
- Lambda permission policy

## 7. Trust Policy

A trust policy is a resource-based policy for roles

Defines:
- Who can assume the role
- Under what conditions
- Each role has exactly one trust policy

## 8. Role Assumption & AWS STS

Roles are assumed using AWS Security Token Service (STS)
- STS returns temporary credentials

Common APIs:
- AssumeRole

## 9. Temporary Security Credentials

Temporary credentials include:
- Access key ID
- Secret access key
- Session token

They:
- Are time-limited
- Automatically expire
- Are more secure than long-term credentials

### 10. Policy Evaluation Logic
- When AWS evaluates a request:
- Explicit DENY – always wins
- Explicit ALLOW
- Implicit DENY (default)
If no policy explicitly allows the action, access is denied.

### 11. Conditions
Conditions add additional checks to policies.
Common condition types:
- IP address restrictions
- MFA requirements
- Time-based access
- Tag-based access control (ABAC)

# 12. Cross-Account Access
- Achieved using IAM roles

Requires:
- Trust policy in target account
- Permission policy in source account
- Root user should never be used

Trust policy controls role assumption




Secure Room:
Who can enter the room- Trust policy 
What can be done inside the room - Permission Policy
