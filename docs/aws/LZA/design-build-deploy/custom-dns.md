# Custom URLs for applications

## Purpose

This document explains how teams can present a custom, friendly URL for an application that already runs behind an internal Application Load Balancer (ALB) in your workload account.

## How CloudFront works (overview)

   • Certificate: Obtain or import a public TLS certificate in ACM (us-east-1) that covers your custom hostname (e.g., app.example.com)
   • Distribution: Create a CloudFront distribution in your workload account, add your alternate domain name (the custom hostname), and select the us-east-1 certificate for viewer TLS
   • Origin: Configure a VPC Origin targeting your internal ALB (same account) and set HTTPS-only from CloudFront to the ALB
   • DNS: In your DNS, point the hostname to the CloudFront distribution (Route 53 ALIAS or external CNAME)

## How API Gateway works (overview)

Use API Gateway as a managed front door with a custom domain for your API. You create the API in your workload account, attach a custom domain, and (for private backends) connect it to your internal ALB via VPC Link
 • Create the API: Prefer HTTP API for new builds; use REST only if you need REST-specific features
 • Private integration: Set up a VPC Link and integrate the API to your internal ALB.
 • Custom domain:
 • Regional: ACM certificate in the same Region as the API; map your stage/base path
 • DNS: Point your hostname to the API Gateway custom domain (Route 53 ALIAS or external CNAME)

## How AppSync (GraphQL) works (overview)

Use AWS AppSync when your application exposes a GraphQL API and you want a single custom hostname for both HTTPS GraphQL and realtime WebSocket traffic
 • Create the API: Define your AppSync GraphQL API (schema, resolvers, auth modes)
 • Custom domain:
 • Request/import an ACM certificate in us-east-1 for your hostname (AppSync custom domains require this)
 • In AppSync, create a Custom Domain and associate it with your API + certificate
 • DNS: Point your hostname to the AppSync custom-domain target (Route 53 ALIAS or external CNAME to the CloudFront domain AppSync provides)
 • Private integration: Use Lambda resolvers placed in private subnets to call your internal ALB.AppSync cannot VPC-link directly to an internal ALB

## References (AWS Docs)

- CloudFront
  - [Add an alternate domain name](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/CreatingCNAME.html)

- API Gateway
  - [Custom domain name for public REST APIs in API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-custom-domains.html)
  - [Custom domain names for HTTP APIs in API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-custom-domain-names.html)
  - [Custom domain names for private APIs in API Gateway](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-private-custom-domains.html)

- AppSync
  - [Configuring custom domain names for GraphQL and real-time APIs](https://docs.aws.amazon.com/appsync/latest/devguide/custom-domain-name.html)
