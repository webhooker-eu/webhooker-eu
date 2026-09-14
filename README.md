<p align="center">
  <img src="https://webhooker.eu/favicon.svg" alt="Webhooker" width="96" />
</p>

<h1 align="center">Webhooker</h1>

<p align="center">
  An EU-hosted inbound webhook gateway — one ingest URL for any provider, with signature
  verification, durable storage, retries, replay and a full delivery history.
</p>

<p align="center">
  <a href="https://webhooker.eu">webhooker.eu</a> ·
  <a href="https://webhooker.eu/docs">Documentation</a> ·
  <a href="https://webhooker.eu/docs/quickstart">Quickstart</a> ·
  <a href="https://webhooker.eu/pricing">Pricing</a> ·
  <a href="https://webhooker.eu/blog">Blog</a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/hosted%20in-EU%20(Germany)-003399" alt="Hosted in the EU" />
  <img src="https://img.shields.io/badge/ingest%20p99-%3C10%20ms-2ea043" alt="Ingest p99 under 10 ms" />
  <img src="https://img.shields.io/badge/delivery-at--least--once-1f6feb" alt="At-least-once delivery" />
  <img src="https://img.shields.io/badge/verification-HMAC--SHA256%20%2F%20SHA1-8250df" alt="Signature verification" />
  <img src="https://img.shields.io/badge/free%20tier-10%2C000%20events%2Fmo-555555" alt="Free tier" />
  <img src="https://img.shields.io/badge/GDPR-DPA%20available-0a7b83" alt="DPA available" />
</p>

---

## What is Webhooker?

Webhooker sits between the services that send you webhooks and the services that consume
them. You give Stripe, GitHub, Shopify — or anything else that sends an HTTP POST — a single
ingest URL. Webhooker verifies the signature, stores the event before it answers the
provider, and delivers it to your own services with retries, a dead letter queue, replay and
a per-attempt log.

Accepting an event and delivering it are separate steps, so a slow or broken destination
never costs you a webhook.

## How a webhook flows through

```
                                  ┌──────────────┐
  Stripe ────┐                    │  verify      │        ┌──► billing-api   (signed)
  GitHub ────┼──► ingest URL ───► │  persist     │ ─────► ├──► slack-relay   (signed)
  Shopify ───┘                    │  200 OK      │        └──► crm-sync      (signed)
             any HTTP POST        └──────┬───────┘
                                         │  retries × backoff · circuit breaker
                                         └──► dead letter queue ──► replay
```

| Stage | What happens |
|---|---|
| **Receive & verify** | The signature is checked against the source's scheme before the payload is accepted. A forged request gets a `401`, is kept for your audit log, and is never delivered. |
| **Persist** | The event is written to durable storage and the provider gets its `200 OK` in single-digit milliseconds. From here on, the event cannot be lost. |
| **Deliver & retry** | Six attempts over about five hours with exponential backoff, status-conditional retries and a per-destination circuit breaker. Anything that still fails lands in a DLQ you can resend from. |

## Features

| Feature | Description |
|---|---|
| **Signature verification** | Per-source HMAC-SHA256 and SHA1 schemes, checked before a payload is accepted. |
| **Smart retries** | Exponential backoff, conditional retries by HTTP status, circuit breaker per destination. |
| **Transformations** | Declarative, per-destination payload reshaping and header injection. |
| **Outbound signing** | Forwarded requests are signed, so your destinations can verify each delivery. |
| **Live tail & replay** | Real-time event stream over SSE, full history, one-click resend of any event. |
| **Alerts & metrics** | Email alerts on delivery failure per source, Prometheus metrics with a latency SLO. |

## EU data residency

Ingest, event storage and delivery all run in Germany, inside the European Union. Payloads,
delivery logs and backups are not mirrored to a US region. Retention limits are enforced —
events expire after 14, 30 or 90 days depending on plan — and a data processing agreement is
available on every paid plan.

## Plans

| | Free | Pro | Team |
|---|---|---|---|
| Events / month | 10,000 | 100,000 | 1,000,000 |
| Sources · destinations | 3 · 9 | 50 · 250 | 200 · 1,000 |
| Event retention | 14 days | 30 days | 90 days |
| Workspace members | 1 | 5 | 25 |
| Price | €0 | €29 / mo | €99 / mo |

Destinations are a shared pool — any source can use as many as it needs. Yearly billing
costs ten months for twelve. Every plan includes verification, retries, transformations,
replay and alerts: you pay for volume and retention, not for features.

## Getting started

1. [Create an account](https://app.webhooker.eu/register) — no card required.
2. Create a source and copy its ingest URL:

```
https://app.webhooker.eu/in/{your-token}
```

3. Paste it into your provider's webhook settings and watch events arrive in the live tail.

The [quickstart](https://webhooker.eu/docs/quickstart) takes four steps, and the
[ingest URL reference](https://webhooker.eu/docs/ingest) lists every response code.

## Documentation

- **Docs:** [webhooker.eu/docs](https://webhooker.eu/docs) — quickstart, ingest reference, sources and destinations
- **Blog:** [webhooker.eu/blog](https://webhooker.eu/blog) — how webhooks behave in production: signature verification, retries, idempotency, replay

## Contact

support@webhooker.eu · [@webhooker.bsky.social](https://bsky.app/profile/webhooker.bsky.social)
