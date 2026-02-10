1. What is AWS Lambda?

AWS Lambda is a serverless compute service that lets you run code without provisioning or managing servers.

Key Characteristics

Event-driven compute service

Pay only for execution time

Automatic scaling

No server management

Stateless by design

2. Core Concepts
2.1 Function

A Lambda Function is your code + configuration.

It contains:

Runtime (Python, Node, Java, etc.)

Handler

Memory settings

Timeout

IAM role

Environment variables

2.2 Execution Environment

Isolated runtime environment

Created on demand

Reused for subsequent requests

Lifecycle:

INIT Phase → INVOKE Phase → SHUTDOWN

2.3 Handler

Entry point of your Lambda function.

Example (Python):

def lambda_handler(event, context):
    return "Hello World"

2.4 Event Source

Anything that triggers Lambda:

API Gateway

S3

SQS

SNS

EventBridge

DynamoDB Streams

3. Lambda Invocation Types
3.1 Synchronous Invocation

Client waits for response

Example: API Gateway → Lambda

aws lambda invoke --function-name myFunction output.json

3.2 Asynchronous Invocation

Event is queued

Lambda processes later

Example: S3 events

Retries:

Automatic retries on failure (2 attempts)

3.3 Event Source Mapping (Polling)

Used for:

SQS

Kinesis

DynamoDB Streams

Lambda polls the source instead of being directly triggered.

4. Concurrency in Lambda
4.1 What is Concurrency?

Concurrency = Number of requests handled at the same time.

Formula:

Concurrency = Requests per second × Avg execution time (seconds)

4.2 Types of Concurrency
Default

1000 per region (soft limit)

Reserved Concurrency

Guarantees capacity

Sets max limit for function

Prevents other functions from using it

Use case: Critical workloads

Provisioned Concurrency

Pre-warms environments

Eliminates cold starts

Costs extra

Use case: Latency-sensitive APIs

4.3 Throttling

Occurs when:

Function exceeds concurrency

Account limit reached

Error:

429 TooManyRequestsException

5. Cold Start vs Warm Start
Cold Start

New execution environment created

Initialization required

Higher latency

Warm Start

Existing environment reused

Faster execution

Provisioned concurrency helps avoid cold starts.

6. Scaling Behavior

Auto scales based on traffic

Can scale up to account limit

Scaling rate:

1000 concurrent executions every 10 seconds per function

7. Lambda Layers
What are Layers?

Reusable packages containing:

Libraries

Dependencies

Config files

Benefits

Smaller deployment packages

Share code across functions

Versioned dependencies

Rules

Max 5 layers per function

Extracted to /opt directory

Only for ZIP-based Lambdas

8. Deployment Packages
Two Types
ZIP-based

Code + dependencies zipped

Max 50 MB (zipped)

Max 250 MB (unzipped)

Container-based

Up to 10 GB

Custom runtimes supported

9. Environment Variables

Key-value pairs

Used for configuration

Encrypted using KMS

Example:

{
  "ENV": "prod",
  "DB_HOST": "xyz"
}

10. IAM and Security
Lambda Execution Role

Grants Lambda permission to access AWS services.

Example:

Read S3

Write DynamoDB

Publish SNS

Best Practices

Follow least privilege

Use separate roles per function

Avoid hardcoding credentials

11. Networking
Default

Runs outside VPC

VPC-Enabled Lambda

Can access private resources

Needs:

Subnets

Security groups

ENI

Drawbacks

Cold start may increase

Requires NAT for internet

12. Monitoring
CloudWatch Metrics

Invocations

Duration

Errors

Throttles

ConcurrentExecutions

CloudWatch Logs

All logs go to:

/aws/lambda/<function-name>

X-Ray Tracing

Distributed tracing

Debug latency

13. Error Handling
Types of Errors

Function errors

Throttles

Timeouts

Permissions errors

Retries
Invocation Type	Retry Behavior
Sync	No retries
Async	2 retries
SQS	Until success or DLQ
DLQ (Dead Letter Queue)

Supported with:

SQS

SNS

14. Timeouts

Default: 3 seconds

Max: 15 minutes

Best practice:

Set minimal required timeout

15. Lambda Destinations

For async invocations:

On Success → SNS/SQS/EventBridge

On Failure → SNS/SQS/Lambda

16. Idempotency in Lambda
Why Important?

Lambda may retry events

Can cause duplicate processing

Solution Patterns

Use idempotency keys

Store processed event IDs

DynamoDB dedup tables

17. Lambda Limits
Item	Limit
Memory	128 MB – 10 GB
Timeout	15 min
Package size	50 MB zip
Container size	10 GB
Layers	5
Concurrency	1000 (default)
18. Integrations
Common Triggers

API Gateway

S3

SQS

SNS

DynamoDB Streams

EventBridge

Step Functions

Kinesis

19. Best Practices

Keep functions small

Use layers for dependencies

Optimize memory settings

Enable structured logging

Implement idempotency

Use environment variables

Avoid large packages

Set proper IAM roles

20. Lambda with API Gateway

Flow:

Client → API Gateway → Lambda → Response


Supports:

REST APIs

HTTP APIs

WebSockets

21. Performance Tuning

Increase memory to increase CPU

Use provisioned concurrency

Reuse DB connections

Use /tmp for caching

22. Versioning and Aliases
Versions

Immutable snapshots of code

Aliases

Point to versions

Example:

dev → v1
prod → v3

23. Cost Model

You pay for:

Number of requests

Duration (GB-seconds)

Free tier:

1M requests

400,000 GB-seconds

24. Serverless Architectures

Common patterns:

Event-driven processing

Microservices

ETL pipelines

Real-time file processing

Chatbots

APIs
