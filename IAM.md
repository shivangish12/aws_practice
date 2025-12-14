IAM users, Root Users
Groups only contain users, not other groups
Components of a request
When a principal tries to use the AWS Management Console, the AWS API, or the AWS CLI, that principal sends a request to AWS. The request includes the following information:
Actions or operations – The actions or operations that the principal wants to perform., such as an action in the AWS Management Console, or an operation in the AWS CLI or AWS API.
Resources – The AWS resource object upon which the principal requests to perform an action or operation.
Principal – The person or application that used an entity (user or role) to send the request. Information about the principal includes the permission policies.
Environment data – Information about the IP address, user agent, SSL enabled status, and the timestamp.
Resource data – Data related to the resource requested, such as a DynamoDB table name or a tag on an Amazon EC2 instance.

IAM Resource
The IAM service stores these resources. You can add, edit, and remove them from the Console.
IAM user
IAM group
IAM role
Permission policy
Identity-provider object

IAM Entity
IAM resources that AWS uses for authentication. Specify the entity as a Principal in a resource-based policy.
IAM user
IAM role

Policy>Group>Entity>Principle

Policies and Permissions-
Trust policy – Defines which principal can assume the role, and under which conditions. A trust policy is a specific type of resource-based policy for IAM roles. A role can have only one trust policy.
Identity-based policies (inline and managed) – These policies define the permissions that the user of the role is able to perform (or is denied from performing), and on which resources.

Roles
