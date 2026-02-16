# Amazon API Gateway

## 1. What is API Gateway?

Amazon API Gateway is a fully managed AWS service used to create,
publish, secure, monitor, and scale APIs.

**Acts as the "front door" for applications**: Client → API Gateway →
Backend (Lambda/EC2/Service)

------------------------------------------------------------------------

## 2. API Types

  API Type        Use Case
  --------------- --------------------------------------
  REST API        Enterprise features, transformations
  HTTP API        Lightweight, fast, low cost
  WebSocket API   Real-time communication

------------------------------------------------------------------------

## 3. REST API (Feature-Rich)

**Supports:** - Mapping templates (VTL) - API Keys & Usage Plans -
Canary deployments - Request validation - Caching - Advanced auth (IAM,
Cognito, Lambda Authorizer)

**Use When:** You need deep control or legacy integration.

------------------------------------------------------------------------

## 4. HTTP API (Modern Choice)

Designed for serverless microservices.

**Benefits:** - Lower cost - Lower latency - Native JWT auth - Simple
configuration

**Limitations:** - No API keys - No caching - No transformations - No
canary deployments

------------------------------------------------------------------------

## 5. WebSocket API

Maintains persistent connection for real-time apps.

**Use Cases:** - Chat applications - Live dashboards - Multiplayer
games - IoT streaming

------------------------------------------------------------------------

## 6. Core Concepts (REST APIs)

API Gateway Structure:

RestApi → Resource → Method → Integration → Backend

  Component     Meaning
  ------------- ----------------
  Resource      Path (/users)
  Method        HTTP action
  Integration   Backend target

------------------------------------------------------------------------

## 7. Integrations

  Type           Description
  -------------- ------------------------
  Lambda Proxy   Pass request as-is
  HTTP           Call external service
  AWS Service    Direct AWS integration
  Mock           Testing

------------------------------------------------------------------------

## 8. Endpoint Types

  Type             Use
  ---------------- -------------------------------
  Edge-Optimized   Global APIs (uses CloudFront)
  Regional         Same-region clients
  Private          Internal VPC-only APIs

------------------------------------------------------------------------

## 9. Security

### TLS (Transport Layer Security)

Encrypts traffic (HTTPS).

### Security Policies

Define allowed TLS versions & ciphers.

------------------------------------------------------------------------

## 10. Authentication Methods

  Method              Use Case
  ------------------- -----------------------
  IAM                 AWS access control
  Cognito             User authentication
  Lambda Authorizer   Custom JWT validation
  JWT (HTTP API)      Built-in validation

------------------------------------------------------------------------

## 11. Throttling & Usage Plans

Protect backend: - Rate limiting - Quotas - API monetization

------------------------------------------------------------------------

## 12. Request Validation

Validate parameters/body before backend invocation to reduce cost.

------------------------------------------------------------------------

## 13. Caching (REST Only)

Improves performance and reduces backend load.

------------------------------------------------------------------------

## 14. Canary Deployments

Gradual rollout of new version.

Example: 90% → Old version\
10% → New version

------------------------------------------------------------------------

## 15. Monitoring

Integrated with CloudWatch: - Latency - Errors - Traffic - Logs

------------------------------------------------------------------------

## 16. Stages

Environment versions: - dev - test - prod

Supports stage variables for dynamic configuration.

------------------------------------------------------------------------

## 17. Custom Domains

Map APIs to your domain: api.company.com

------------------------------------------------------------------------

## 18. CORS

Required for browser apps. Configure Access-Control-Allow-Origin.

------------------------------------------------------------------------

## 19. Private APIs

Accessible only via VPC Endpoint (PrivateLink).

------------------------------------------------------------------------

## 20. Developer Portal

Self-service portal for external developers: - Discover APIs - Generate
API keys - Read documentation - Subscribe to usage plans

------------------------------------------------------------------------

## 21. REST vs HTTP vs WebSocket

  Feature             REST     HTTP    WebSocket
  ------------------- -------- ------- -----------
  Advanced Features   Yes      No      No
  Cost                Higher   Lower   Medium
  Real-Time           No       No      Yes

------------------------------------------------------------------------

## 22. Best Practices

-   Use HTTP APIs for microservices
-   Use REST APIs for advanced control
-   Enable throttling
-   Validate requests early
-   Monitor with CloudWatch
-   Use Regional endpoints unless global needed




