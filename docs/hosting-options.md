# Hosting & Platform Options Reference (April 2026)

Filtered for: **CLI-first workflows, low cost (dominant), flexible/feature-rich, general-good security hygiene, web + API both in scope**. Turnkey site builders and no-code app builders excluded by request.

Cost is the tie-breaker across all other factors. Within each category, options are listed roughly cheapest-floor first. Pricing USD unless noted; verified April 2026, flagged where sources conflict.

---

## 1. Static / JAMstack Hosts

BYO repo, they build and serve. Static by default, most add serverless functions. All support git integration + preview deploys.

### Cloudflare Pages

Static + edge-functions on Cloudflare's network. Unlimited sites/requests/bandwidth on all tiers; pair with Workers for dynamic logic.

| Tier | Price | Builds/mo | Concurrent | Domains/project |
|---|---|---|---|---|
| Free | $0 | 500 | 1 | 100 |
| Pro | $20/mo annual or $25/mo | 5,000 | 5 | 250 |
| Business | $200/mo annual | 20,000 | 20 | 500 |

- **CLI/deploy:** `wrangler pages deploy` or `wrangler pages dev`; also git integration. Terraform provider available.
- **Long-lived:** Workers support WebSockets + Durable Objects + Queues.
- **Gotchas:** 20-min build cap per build; Functions billed under Workers separately; analytics/logs thinner than Vercel/Netlify.

### GitHub Pages

Static-only, free, tied to a GitHub repo. Jekyll built in, other SSGs via Actions. No serverless, no rewrites without workarounds.

| Limit | Value |
|---|---|
| Repo size (soft) | 1GB |
| Bandwidth (soft) | 100GB/mo |
| Builds | 10/hour |
| Price | Free (public); private repo publishing requires GitHub Pro ~$4/mo (verify current pricing) |

- **CLI/deploy:** `git push`; `gh` CLI manages repos/Actions.
- **Long-lived:** None.
- **Gotchas:** Soft-limit warnings then hard cutoffs for abuse; no server-side code; no redirects/rewrites.

### Netlify

Original JAMstack host. Credit-based pricing. Commercial use allowed on free (unlike Vercel). Forms, Identity, Functions built in.

| Tier | Price | Credits/mo | Concurrent builds |
|---|---|---|---|
| Free | $0 | 300 | 1 |
| Pro | $20/mo | 3,000 | 3+ |
| Enterprise | Custom | Unlimited | Custom |

- **CLI/deploy:** `netlify` CLI (`netlify deploy`, `netlify dev`); git integration.
- **Long-lived:** Background Functions (15 min), no WebSockets.
- **Gotchas:** Credit conversion opaque (~20 credits/GB BW); $10/1,500-credit overage packs; "unlimited build minutes" is really a credit budget.

### Vercel

Next.js creator's platform. Polished DX, first-class Next.js features (ISR, Edge, Middleware). Hobby is personal/non-commercial only; commercial work requires Pro.

| Tier | Price | Bandwidth | Functions | Seats |
|---|---|---|---|---|
| Hobby | $0 | 100GB | 1M invocations, 4 CPU hrs | 1 |
| Pro | $20/user/mo + usage | 1TB ($0.15/GB over) | 10M + usage | $20/seat |
| Enterprise | Custom | Custom | Custom | Custom |

- **CLI/deploy:** `vercel` CLI; `vercel deploy`, `vercel dev`, `vercel env`. Git integration first-class.
- **Long-lived:** Limited (Edge runtime supports streaming; long WebSockets require Fluid compute or external).
- **Gotchas:** Hobby forbids client/commercial work; image optimization + BW overages spike on traffic; default spend cap $200/mo (configurable).

### Deno Deploy

Static + serverless from the Deno team. TypeScript-first, KV included, global edge.

| Tier | Price | Requests/mo | Bandwidth | KV |
|---|---|---|---|---|
| Free | $0 | 1M | 20GB egress | 1GB |
| Pro | $20/mo | 5M (+$2/M) | 200GB (+$0.50/GB) | 5GB |
| Builder | $200/mo | 20M | 300GB | 10GB |

- **CLI/deploy:** `deployctl deploy`; git integration.
- **Long-lived:** Classic platform sunsets July 2026; new platform on console.deno.com supports streaming.
- **Gotchas:** Smaller ecosystem than Node hosts; some npm packages need compat shims; less cost-efficient than Workers at scale.

---

## 2. Full-Stack App Platforms (PaaS)

Git push, they run your backend. Containers, DBs, workers. Less ops than VPS, more flexibility than JAMstack.

### Fly.io

Firecracker micro-VMs, global regions, scale-to-zero. Pay-as-you-go only; no free tier since late 2024. Very CLI-first culture.

| Resource | Price |
|---|---|
| shared-cpu-1x 256MB | ~$1.94/mo always-on |
| shared-cpu-1x 1GB | ~$6.79/mo always-on |
| Volume | $0.15/GB-mo |
| Egress | $0.02/GB (US/EU), higher elsewhere |
| Support (optional) | Scale $29/mo, Premium $99/mo |
| Managed Postgres (Launch) | ~$282/mo |

- **CLI/deploy:** `flyctl` (aka `fly`); everything is CLI/TOML-driven (`fly launch`, `fly deploy`, `fly ssh console`).
- **Long-lived:** Full support — WebSockets, long-running processes, workers, cron via Machines.
- **Gotchas:** No free tier (2 VM-hr / 7-day trial); managed Postgres pricey — most people roll their own; pricing page vague, use the calculator.

### DigitalOcean App Platform

Heroku-style on DO. Simpler pricing than Heroku; fewer features than Render/Railway. Git or Docker deploys.

| Tier | Price | Included |
|---|---|---|
| Free static | $0 | 3 static apps, 1 GiB transfer each |
| Basic shared CPU | $5–$10/mo | 512MB–1GB RAM |
| Shared scalable | $12–$50/mo | 1–2 vCPU, 1–4GB |
| Dev DB (Postgres) | $7/mo | 512MB |
| Extra static apps | $3/mo each | |

Egress: $0.02/GiB over included.

- **CLI/deploy:** `doctl apps create --spec app.yaml`; git integration.
- **Long-lived:** WebSockets + workers supported; no scale-to-zero.
- **Gotchas:** Fewer levers than Render/Railway (no preview envs on low tiers); prod-grade managed DB adds ~$15/mo; slow build minutes.

### Railway

Modern Heroku-ish PaaS. Per-second billing, one-click Postgres/Redis/MySQL/Mongo, preview envs.

| Tier | Monthly min | Resource cap/service |
|---|---|---|
| Trial | $0 (30-day, $5 credit) | 1 vCPU, 0.5GB RAM |
| Hobby | $5/mo (incl. $5 credit) | 48 vCPU / 48GB RAM |
| Pro | $20/mo (incl. $20 credit) | 1,000 vCPU / 1TB RAM |
| Enterprise | Custom | 2,400+ vCPU |

Usage: ~$20/vCPU-mo always-on, RAM $0.0000039/GB-sec, egress $0.05/GB.

- **CLI/deploy:** `railway` CLI (`railway up`, `railway run`, `railway link`); git integration.
- **Long-lived:** Full support (WebSockets, cron, workers).
- **Gotchas:** No free always-on; egress adds up on media-heavy apps; idle detection off by default (stopped services still bill).

### Render

Heroku-style with a real free tier (services spin down; Postgres expires at 30 days). Autoscaling, managed Postgres/Redis, cron, preview envs.

| Tier | Price | Notes |
|---|---|---|
| Free | $0 | Web service spins down after 15min; Postgres expires 30 days |
| Hobby | $0 + usage | 1 project, 100GB BW, 2 custom domains |
| Professional | $19/user/mo | Unlimited projects, 500GB BW, autoscaling |
| Organization | $29/user/mo | 1TB BW, unlimited seats, SOC 2 |

Instances: Starter $7/mo (512MB), Standard $25/mo (2GB), up to Pro Ultra $450/mo. Postgres: Basic $6/mo to $6,200/mo. Key Value (Redis): $0–$1,100/mo.

- **CLI/deploy:** `render` CLI (still newer); primary workflow is git + `render.yaml` blueprints.
- **Long-lived:** WebSockets, workers, cron supported.
- **Gotchas:** Free services cold-start noticeably; free Postgres hard-expires at 30 days; per-user seat pricing bites small teams.

### Heroku

The original PaaS. Free tier removed Nov 2022. Largest add-on marketplace.

| Tier | Price | Notes |
|---|---|---|
| Eco | $5/mo | Pool of 1,000 dyno-hours, sleeps when idle |
| Basic | $7/mo/dyno | Always-on, 512MB |
| Standard-1X | $25/mo/dyno | 512MB, metrics |
| Standard-2X | $50/mo/dyno | 1GB |
| Performance-M | $250/mo/dyno | 2.5GB dedicated |

Postgres Mini $5/mo, Basic $9/mo, Standard-0 $50/mo. Redis Mini $3/mo, Premium-0 $15/mo.

- **CLI/deploy:** `heroku` CLI (`heroku create`, `heroku ps`, `heroku logs`); git push to `heroku` remote.
- **Long-lived:** WebSockets, workers (Procfile), scheduled jobs.
- **Gotchas:** Expensive per-unit vs modern alternatives; add-ons (logs, Redis, PG, SSL) push real cost 2–3x headline; no free tier.

---

## 3. Serverless / Edge Compute

FaaS. Billed per invocation + duration. Good for low-volume APIs, webhooks, cron, glue code.

### Cloudflare Workers

V8-isolate edge functions. ~0ms cold starts. KV, D1 (SQLite), R2 (S3-compat, zero egress), Durable Objects, WebSockets, cron, queues.

| Tier | Price | Included |
|---|---|---|
| Free | $0 | 100K req/day, 10ms CPU/invocation |
| Paid | $5/mo min | 10M req/mo (+$0.30/M), 30M CPU-ms (+$0.02/M) |

Storage: KV $0.50/GB-mo, D1 $0.75/GB-mo, R2 $0.015/GB-mo (free egress).

- **CLI/deploy:** `wrangler` CLI (`wrangler init`, `wrangler dev`, `wrangler deploy`); `wrangler.toml` config.
- **Long-lived:** WebSockets + Durable Objects (stateful); Queues for async; Cron Triggers.
- **Gotchas:** 10ms CPU cap on free; V8 isolates, not Node.js — some npm packages break; D1 still maturing.

### AWS Lambda

The incumbent. Cheapest at scale, most complex to wire. 15-min max, up to 10GB RAM, Graviton ARM ~20% cheaper.

| Item | Price |
|---|---|
| Requests | $0.20/1M |
| Duration (x86) | $0.0000166667/GB-sec |
| Duration (ARM) | ~20% less |
| Free tier | 1M req + 400K GB-sec/mo (perpetual) |

- **CLI/deploy:** `aws` CLI, AWS SAM, CDK, Serverless Framework, Terraform — all CLI/IaC-native.
- **Long-lived:** No — 15-min execution max; for WebSockets use API Gateway WebSocket APIs (separate billing).
- **Gotchas:** Lambda is cheap but peripherals (API Gateway, CloudWatch, NAT, data transfer) bill separately and stack up; VPC cold starts worse; IAM is a project; locks you to AWS.

### Vercel Functions

Lambda-compatible + Edge runtime, tied to Vercel deployments. Pricing is part of the Vercel plan, not standalone.

| Tier | Requests | Active CPU |
|---|---|---|
| Hobby | 1M/mo | 4 hrs/mo |
| Pro | 10M incl, then $0.60/M | $0.128/active-CPU-hr |

- **CLI/deploy:** `vercel` CLI; functions live in repo routes.
- **Long-lived:** Streaming supported; Fluid compute improves but WebSocket-heavy workloads fit Workers/Fly better.
- **Gotchas:** "Active CPU" + "Edge Requests" billing model rewrote in 2024–25; Functions+BW+Builds surprise on bills; Hobby non-commercial.

### Deno Deploy (as serverless)

Same pricing as Static section. Strong TypeScript story, KV included. `deployctl` CLI.

---

## 4. BaaS / Managed Databases

Auth + DB + storage + realtime without writing a backend from scratch. Trade: vendor lock-in, per-MAU/per-GB cost creep.

### Turso

SQLite-compat edge DB on libSQL. Regional read replicas, cheap reads. PITR on paid.

| Tier | Price | DBs | Storage | Rows Read | Rows Written |
|---|---|---|---|---|---|
| Free | $0 | 100 | 5GB | 500M/mo | 10M/mo |
| Developer | $4.99/mo | Unlimited | 9GB | 2.5B | 25M |
| Scaler | $24.92/mo | Unlimited | 24GB | 100B | 100M |
| Pro | $416/mo | Unlimited | 50GB | 250B | 250M |

- **CLI/deploy:** `turso` CLI (`turso db create`, `turso db shell`, auth, replica mgmt).
- **Security:** Token-scoped DB access, per-DB read/write tokens.
- **Gotchas:** SQLite single-writer limits write-heavy workloads; libSQL tooling smaller than Postgres; some Prisma features not fully supported.

### Neon

Serverless Postgres with copy-on-write branching, scale-to-zero compute. PG 16/17, PITR.

| Tier | Price | Storage | Compute | Branches |
|---|---|---|---|---|
| Free | $0 | 0.5GB/project | 100 CU-hrs | 10 |
| Launch | Usage | $0.35/GB-mo | $0.106/CU-hr | 10 (+$1.50/extra) |
| Scale | Usage | $0.35/GB-mo | $0.222/CU-hr | 25 |

Paid tiers include 100GB public egress.

- **CLI/deploy:** `neonctl` (now `neon` CLI); git-branch-like DB branching via CLI.
- **Security:** IP allowlists, SOC 2 on paid, scoped connection strings per branch/role.
- **Gotchas:** CU-hour billing opaque until you watch it (1 CU ≈ 1 vCPU + 4GB RAM); free compute suspends at limit; extra branches cost per branch-month.

### Supabase

Open-source Postgres-based Firebase alternative. Postgres + RLS + Realtime, Auth, S3-compat Storage, Edge Functions (Deno). Self-hostable.

| Tier | Price | DB | MAU | Storage | Egress |
|---|---|---|---|---|---|
| Free | $0 | 500MB | 50K | 1GB | 5GB |
| Pro | $25/mo | 8GB | 100K | 100GB | 250GB |
| Team | $599/mo | 8GB+ | 100K+ | 100GB+ | 250GB+ |
| Enterprise | Custom | Custom | Custom | Custom | Custom |

Over-limits: MAU $0.00325, egress $0.09/GB, storage $0.021/GB-mo, compute $10–$3,730/mo.

- **CLI/deploy:** `supabase` CLI (`supabase init`, `supabase db push`, `supabase functions deploy`); local dev via Docker.
- **Security:** Row-Level Security is core; SSO on Team+; self-host gives you full control.
- **Gotchas:** Free projects pause after 7 days inactivity (restorable but breaks demos); egress is the silent cost killer; compute upgrades become mandatory as DB grows — "$25" baseline easily becomes $60–$120.

### Appwrite

Open-source Firebase alternative. Self-host via Docker or their cloud. Auth, document DB, Storage, Functions, Realtime, Messaging.

| Tier | Price | Bandwidth | Storage | Executions | MAU |
|---|---|---|---|---|---|
| Free | $0 | 5GB | 2GB | 750K | 75K |
| Pro | $25/mo | 2TB | 150GB | 3.5M | 200K |
| Enterprise | Custom | Custom | Custom | Custom | Custom |

- **CLI/deploy:** `appwrite` CLI for cloud + self-hosted.
- **Security:** OSS — fully auditable; team/permission model built in.
- **Gotchas:** Free caps at 2 projects with shared resources; self-host is doable but non-trivial; document DB only (no SQL).

### Firebase

Google's BaaS. Firestore, Auth, Cloud Functions, Hosting, Storage. Spark (free) or Blaze (pay-as-you-go); no flat paid tier.

| Tier | Price | Firestore free/day |
|---|---|---|
| Spark | $0 | 50K reads, 20K writes, 1GiB |
| Blaze | $0 + usage | Same free quota, then metered |

Blaze: Firestore $0.18/100K reads, $0.18/100K writes, $0.02/100K deletes, $0.26/GB-mo. Storage $0.026/GB-mo + $0.15/GB egress.

- **CLI/deploy:** `firebase` CLI — comprehensive (`firebase deploy`, emulators for local dev).
- **Security:** Firestore Security Rules DSL is flexible but a foot-gun; App Check, Auth providers managed.
- **Gotchas:** Firestore cost scales with read count (denormalized design explodes bills); Cloud Storage default bucket requires Blaze as of Feb 2026; no self-host path.

### Convex

Reactive TS-native backend. Queries = real-time subscriptions. End-to-end types, crons, file storage, vector search. Document-style, not SQL.

| Tier | Price | Function calls | DB | Bandwidth |
|---|---|---|---|---|
| Starter | $0 | 1M/mo | 0.5GB | 1GB |
| Professional | $25/dev/mo | 25M/mo | 50GB | 50GB |
| Enterprise | $2,500/mo min | Custom | Custom | Custom |

- **CLI/deploy:** `npx convex dev` / `npx convex deploy`.
- **Security:** Auth integrates with Clerk/Auth0; per-function access rules in TS.
- **Gotchas:** Per-dev Pro pricing unusual; migration off is non-trivial (proprietary queries); smaller ecosystem than Supabase/Firebase.

### PlanetScale

MySQL-compat Vitess. **Free Hobby tier removed April 2024** — all DBs now on paid Scaler Pro minimum.

| Tier | Price | Notes |
|---|---|---|
| Scaler Pro (PS-10) | $39/mo min | Entry production (verify current pricing) |
| Scaler+ | ~$79/mo | Higher resources |
| Enterprise | Custom | SSO, audit logs |

- **CLI/deploy:** `pscale` CLI (branching, deploy requests, shell).
- **Security:** IP allowlisting, branch-scoped credentials, SOC 2.
- **Gotchas:** No free tier; pricing has reset multiple times — verify current; no FK constraints (Vitess limit) is a real app-design constraint.

### Xata

Postgres + search + file attachments. Pivoted to Postgres-native in 2024.

| Tier | Price | Notes |
|---|---|---|
| Open Source | Free | Self-managed on K8s |
| Standard | $0.012/hr + storage | $100/14-day trial credit |
| Enterprise | Custom | BYOC |

Storage: EBS $0.17/GB-mo (soon), Premium NVMe $0.30/GB-mo. Instances $9–$1,121/mo.

- **CLI/deploy:** `xata` CLI.
- **Security:** BYOC option, IP allowlists on paid.
- **Gotchas:** Older tutorials stale from the Postgres pivot; hourly + storage pricing makes monthly floor hard to predict; verify current free/trial terms.

---

## 5. VPS / Self-Hosted

Raw Linux box. Cheapest $/compute; you own all ops (nginx/Caddy, systemd, certbot, backups, monitoring, patching). Pairs well with Docker, Coolify, Dokku, Dokploy, k3s.

### Vultr

US-based, 30+ regions. IPv6-only tier is industry's cheapest. Block storage $0.10/GB-mo.

| Plan | Price | vCPU | RAM | Storage | BW |
|---|---|---|---|---|---|
| Regular IPv6-only | $2.50/mo | 1 | 512MB | 10GB | 500GB |
| Regular 1GB | $3.50/mo | 1 | 512MB | 10GB | 500GB |
| High Freq 1GB | $6/mo | 1 | 1GB | 32GB | 1TB |
| High Perf 1GB | $6/mo | 1 | 1GB | 25GB | 2TB |

- **CLI/deploy:** `vultr-cli`; Terraform provider; cloud-init supported.
- **Security:** Firewall groups, private networking, DDoS on select instances.
- **Gotchas:** IPv6-only needs a proxy or IPv4 add-on for most client access; Freq vs Performance naming confuses; snapshots billed separately.

### Hetzner Cloud

German provider with exceptional $/perf. April 2026 price hike (~30–35%) but still best-in-class. EU + US (Ashburn, Hillsboro). ~20TB/mo traffic typical.

| Plan | Price (EUR/mo) | vCPU | RAM | Storage | Arch |
|---|---|---|---|---|---|
| CX22 | EUR 3.79 | 2 shared | 4GB | 40GB | x86 |
| CAX11 | EUR 4.49 | 2 shared | 4GB | 40GB | ARM |
| CPX11 | EUR 5.99 | 2 shared | 2GB | 40GB | AMD |

- **CLI/deploy:** `hcloud` CLI; Terraform; cloud-init; Packer.
- **Security:** Cloud Firewalls, private networks, Cloud Volumes encrypted at rest.
- **Gotchas:** Signup painful (ID verification, occasional rejects); April 2026 hike reduced but didn't erase $/perf edge; US DC choice limited; no managed Postgres.

### DigitalOcean Droplets

Approachable VPS. Strong docs, managed DB/Spaces add-ons. Per-second billing since Jan 2026.

| Droplet | Price | vCPU | RAM | Storage | Transfer |
|---|---|---|---|---|---|
| Basic 512MB | $4/mo | 1 | 512MB | 10GB | 500 GiB |
| Basic 1GB | $6/mo | 1 | 1GB | 25GB | 1 TiB |
| Basic 2GB | $12–18/mo | 1–2 | 2GB | 50–60GB | 2–3 TiB |
| Premium 8GB | $48/mo | 4 | 8GB | 160GB | 5 TiB |

- **CLI/deploy:** `doctl`; Terraform; cloud-init.
- **Security:** Cloud firewalls free; VPC free; encrypted block storage.
- **Gotchas:** 512MB OOMs on modern Node/Python; managed DBs start ~$15/mo; overage $0.01/GiB.

### Linode / Akamai Cloud

Linode acquired by Akamai; rebrand ongoing. Managed DB, LKE (K8s), Object Storage, NodeBalancer LB $10/mo.

| Plan | Price | vCPU | RAM | Storage | Transfer |
|---|---|---|---|---|---|
| Nanode 1GB | $5/mo | 1 | 1GB | 25GB | 1TB |
| Shared 2GB | $12/mo | 1 | 2GB | 50GB | 2TB |
| Shared 4GB | $20/mo | 2 | 4GB | 80GB | 4TB |

- **CLI/deploy:** `linode-cli`; Terraform; Ansible modules.
- **Security:** Cloud Firewall, VLANs, encrypted disks.
- **Gotchas:** Rebrand in flux — URLs shift between linode.com / akamai.com; community smaller post-acquisition.

### OVH / OVHcloud

French provider, low headline prices, rougher support. Strong EU, smaller US. VPS + bare metal + OpenStack. Anti-DDoS included.

| Plan | Price | vCPU | RAM | Storage |
|---|---|---|---|---|
| VPS-1 (2026) | $7.60/mo | 1 | 2GB | 20GB NVMe |
| Higher VPS | $12–$40/mo | 2–8 | 4–16GB | 80–320GB |

- **CLI/deploy:** `ovhai` CLI (for AI products) + API; first-class Terraform provider.
- **Security:** Anti-DDoS included; EU data sovereignty focus.
- **Gotchas:** April 2026 hike affected VPS 2026 range; support response times variable; EU compliance context differs from US providers.

---

## 6. Domains + DNS + Email

### Domain Registrars

| Registrar | .com (first/renew) | WHOIS Privacy | Notes |
|---|---|---|---|
| Cloudflare Registrar | ~$10.44/yr (at-cost) | Free | No markup; must use CF DNS; no email hosting |
| Porkbun | $11.08/yr | Free | Generous TLD pricing, free SSL + email forward, solid UI/API |
| Namecheap | $9.58 first / $13.98 renew | Free | Heavier upsell; good support; frequent promos |

- **CLI/API:** Cloudflare — full API + Terraform provider. Porkbun — REST API, several community CLIs. Namecheap — REST API, community wrappers.
- **Gotchas:** Cloudflare requires CF DNS on the domain, has a transfer-out cooldown, and restricts new registration on some TLDs (transfer/renewal only). Porkbun premium TLDs (e.g. .io) marked up less than Namecheap but not at-cost. Namecheap first-year discount renews much higher.

### DNS

All three registrars include DNS. Splitting is legit (register at Porkbun, DNS at Cloudflare for free WAF/Workers). Cloudflare's free DNS has no practical query cap and a full API/Terraform story.

### Email Routing / Mailboxes

| Service | Price | Mailbox | Send | Notes |
|---|---|---|---|---|
| Cloudflare Email Routing | Free | No (fwd only) | No | Unlimited aliases/catch-all |
| Zoho Mail Free | Free | 5 users, 5GB | Web only | No IMAP/POP; region-restricted signup |
| Zoho Mail Lite | $1/user/mo | 5–10GB | Yes | IMAP/POP unlocked |
| Zoho Mail Premium | ~$4/user/mo | 50GB | Yes | Verify current pricing |
| Fastmail Biz Basic | $4/user/mo | 5GB | Yes | Privacy-focused, JMAP |
| Fastmail Biz Standard | $6/user/mo | 50GB | Yes | Custom domain |
| Fastmail Biz Professional | $10/user/mo | 100GB | Yes | Third-party app access |
| Google Workspace Starter | $7 annual / $8.40 monthly | 30GB pooled | Yes | Gmail, Drive, Meet, Gemini |
| Google Workspace Standard | $14 annual / $16.80 monthly | 2TB | Yes | |

- **Gotchas:** CF Email Routing is forward-only — can't send from `you@domain` through CF (pair with any SMTP). Zoho Free needs DNS ownership and is region-restricted; no mobile IMAP. Google Workspace bills per user regardless of usage. Fastmail is email-only (no docs suite).

---

## How the Categories Differ

| Category | You write code? | Has a DB? | WebSockets/long-lived? | Typical monthly floor |
|---|---|---|---|---|
| 1. Static / JAMstack | Yes (repo + build) | Via integrations only | Via functions (limited) | $0 |
| 2. Full-Stack PaaS | Yes (app code) | Yes (add-on) | Yes | $5–$25 |
| 3. Serverless / Edge | Yes (functions) | Via integrations (KV/D1/etc) | Workers yes; Lambda limited | $0–$5 |
| 4. BaaS / Managed DB | Yes (frontend + SDK) | Yes (core) | Yes (Realtime, Convex subs) | $0–$25 |
| 5. VPS / Self-hosted | Yes (everything) | You install it | Yes (anything you want) | $3–$10 |
| 6. Domain / DNS / Email | N/A | N/A | N/A | $0–$15 |

"Monthly floor" = cheapest realistic always-on config, not trial pricing. Categories combine freely (e.g. Cloudflare Pages + Supabase + Cloudflare Registrar; Hetzner VPS + Caddy + self-hosted Appwrite; Vultr + Docker + Neon).

---

*Pricing verified against vendor pages + secondary aggregators, April 2026. Hardest-to-verify figures: PlanetScale entry tier (conflicting sources), Xata (post-pivot flux), Zoho Mail Premium, Fly.io (no clean plan table), GitHub Pages private-repo publishing. Check the vendor page before committing.*
