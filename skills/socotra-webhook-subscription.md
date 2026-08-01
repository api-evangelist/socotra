---
name: Socotra webhook subscription
description: Register, inspect, and repair tenant webhook subscriptions so an agent receives Socotra platform events reliably.
api: openapi/socotra-openapi-original.json
operations: [createAuthToken, createWebhook, fetchWebhooks, fetchWebhook, updateWebhook, unsuspendWebhook, resendDivertedEvent]
---

# Socotra webhook subscription

Use this skill to subscribe to Socotra platform events over HTTP webhooks and to
recover undelivered ("diverted") events. All operations are scoped to a
`{tenantLocator}`.

## Auth
Mint a bearer token with `POST /auth/users/tokens` (`createAuthToken`) and send it as
`Authorization: Bearer <token>`.

## Steps
1. **Discover event types** — read the per-domain event definitions, e.g.
   `GET /policy/events` (`fetchPolicyEventDefinitions`),
   `GET /billing/events` (`fetchBillingEventDefinitions`),
   `GET /claim/events` (`fetchClaimEvents`).
2. **Create a subscription** — `POST /event/{tenantLocator}/webhooks`
   (`createWebhook`) with your receiver URL and the events you want.
3. **List / inspect** — `GET /event/{tenantLocator}/webhooks/list`
   (`fetchWebhooks`) and `GET /event/{tenantLocator}/webhooks/{webhookLocator}`
   (`fetchWebhook`).
4. **Update** — `PATCH /event/{tenantLocator}/webhooks/{webhookLocator}`
   (`updateWebhook`) to change the target or filter.
5. **Recover** — if a webhook is suspended after repeated failures,
   `PATCH .../{webhookLocator}/unsuspend` (`unsuspendWebhook`), then re-deliver
   diverted events with
   `POST .../{webhookLocator}/diverted/{eventLocator}/resend` (`resendDivertedEvent`).

## Rules
- Failed deliveries are captured as "diverted" events per webhook; drain them with
  the diverted-list + resend operations rather than assuming at-most-once delivery.
- A pull-based alternative to webhooks is the Socotra Event Stream (`@socotra/events-cli`).
