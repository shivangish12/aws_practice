AWS App Runner is a fully managed service offered by Amazon Web Services that enables developers to build, deploy, and run web applications and APIs directly from source code repositories or container images without managing infrastructure.

It abstracts server provisioning, load balancing, scaling, and TLS configuration, allowing developers to focus on application development rather than infrastructure management.

# Deployment Options
## Source Code Deployment

Connects to repositories such as GitHub or Bitbucket.

Automatically builds a container using managed runtimes (for example, Python or Node.js).

Supports automatic redeployment when code changes are pushed.

## Container Image Deployment

Deploys pre-built container images from Amazon ECR (public or private).

Skips the build stage and directly runs the container image.

# Managed Infrastructure

When a service is created, AWS App Runner automatically:

Builds and deploys the application

Provisions compute resources

Configures load balancing

Provides HTTPS endpoints with managed TLS certificates

Automatically scales instances based on traffic

Integrates with Amazon CloudWatch for logging and monitoring

This significantly reduces operational overhead.

# Auto Scaling

AWS App Runner dynamically adjusts the number of running container instances based on incoming request volume. It scales up during traffic spikes and scales down during periods of low demand to optimize cost.

# Networking and Security

Provides automatic HTTPS endpoints.

Supports VPC connectors to securely access private AWS resources such as databases.

Integrates with IAM for access control.

# Configuration Using apprunner.yaml

An optional configuration file, apprunner.yaml, allows customization of:

Build commands

Runtime versions

Environment variables

Application start commands

Port configuration

This enables consistent and reproducible deployments.

# Pricing Model

Pricing is based on:

Compute resources consumed (CPU and memory)

Active service runtime

Services can be paused to reduce costs when not in use.

# Use Cases

AWS App Runner is suitable for:

HTTP/HTTPS web applications

APIs requiring automatic scaling

Rapid deployment from source code or containers

Teams that prefer minimal infrastructure management
