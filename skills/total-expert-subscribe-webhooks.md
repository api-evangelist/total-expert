---
name: Subscribe to Total Expert webhooks
description: Register an HTTPS endpoint for entity/verb actions and process notifications using the hashValue change-detection contract.
api: openapi/total-expert-openapi.yml
operations: [fetchWebhookActions, createWebhookSubscription, fetchWebhookSubscriptions, updateWebhookSubscription, deleteWebhookSubscription]
generated: '2026-07-21'
method: generated
---

# Subscribe to Total Expert webhooks

## Discover and subscribe
1. GET `/v1/webhook-actions` (`fetchWebhookActions`) to list the subscribable actions — each is an entity + verb pair (e.g. `CONTACT` / `CREATED`).
2. POST `/v1/webhook-subscriptions` (`createWebhookSubscription`) with:
   - `url`: your HTTPS endpoint (must have a valid certificate),
   - `actions`: the list of actions to subscribe (one-to-many relationship),
   - `secret`: a randomly generated 32-64 char hex string (up to 255 chars supported).
3. Manage subscriptions with `fetchWebhookSubscriptions` / `updateWebhookSubscription` / `deleteWebhookSubscription`. Rotate the secret via PATCH; unsubscribe individual actions with the special `remove_actions` attribute.

## Process notifications
4. Each notification is an HTTP POST with body fields `item`, `action`, `id`, `hashValue`, `secret`.
5. Authenticate the sender by comparing `secret` to the value you registered — reject mismatches.
6. Deduplicate with `hashValue`: it is a hex digest of the affected record, unchanged when nothing actually changed, so skip notifications whose hash matches the most recent one you processed for that record.
7. Fetch the full record on real changes (e.g. GET `/v1/contacts/{contact_id}` for `CONTACT` items) using your cached OAuth token.

## Rules
- Notifications only fire for records associated with your organization.
- Webhook surface reference: `asyncapi/total-expert-webhooks.yml`.
