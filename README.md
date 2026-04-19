# sjdc.ai

Personal site and showcase for custom SaaS-style service work.

## Planned stack

AWS-native serverless. Chosen for existing AWS familiarity, low-volume cost floor under $15/mo, and headroom to grow into more complex service implementations.

| Layer | Service |
|---|---|
| Static site hosting | S3 + CloudFront |
| HTTPS / CDN | CloudFront (ACM cert) |
| APIs | Lambda (Function URLs or API Gateway HTTP) |
| Database | DynamoDB (on-demand) |
| DNS | Route 53 or Cloudflare |
| IaC / deploy | SST (candidate) — CLI-first |

Expected monthly cost at < 1,000 pageviews + < 500 API calls: **~$0–$1/mo** (Route 53 hosted zone if used; otherwise effectively $0 on perpetual free tiers). CloudWatch Logs retention and VPC/NAT Gateway are the known cost traps to avoid.

## Docs

- [Hosting options reviewed](docs/hosting-options.md) — neutral feature/cost breakdown of alternatives considered (static hosts, PaaS, serverless, BaaS/DB, VPS, domains/email).
