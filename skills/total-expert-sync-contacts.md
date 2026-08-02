---
name: Sync contacts into Total Expert
description: Create or update contacts in Total Expert from an external system, honoring authentication, deduplication, ownership, and pagination rules.
api: openapi/total-expert-openapi.yml
operations: [fetchATokenFromClientCredentialsGrant, fetchHeartbeat, fetchContacts, fetchContact, createContactAsAdmin, updateContact, fetchContactGroups]
generated: '2026-07-21'
method: generated
---

# Sync contacts into Total Expert

## Authenticate
1. POST `/v1/token` (`fetchATokenFromClientCredentialsGrant`) with an `Authorization: Basic base64(client_id:client_secret)` header and body `{"grant_type": "client_credentials"}`. The response gives a Bearer `access_token` (`expires_in` 3600).
2. Cache the token — the token endpoint allows only 2 requests per hour. Refresh only when a call returns 401.
3. Verify access with GET `/v1/heartbeat` (`fetchHeartbeat`).

## Create or update
4. Client-credentials tokens act **As Admin**: every contact you create must specify an `owner` relationship (by `id`, `username`, `email`, or external id).
5. POST `/v1/contacts` (`createContactAsAdmin`). Supply the required fields — `first_name`, `last_name`, `source`, plus at least one of email, full address, or a phone number — or the API rejects the contact.
6. Rely on deduplication instead of pre-checking: if an existing contact matches (per-user matching on the required fields), the POST is treated as a PATCH to that record, so re-sending is safe and never duplicates.
7. For targeted changes to a known record use PATCH `/v1/contacts/{contact_id}` (`updateContact`).
8. Include any of the customer's "Required Groups" when creating contacts; list groups with GET `/v1/contact-groups` (`fetchContactGroups`).

## Read back
9. Page through GET `/v1/contacts` (`fetchContacts`) with `page[number]`/`page[size]` (default size 10); follow the `links.next` page number until it is null.

## Rules
- All requests are JSON; dates use `"YYYY-MM-DD hh:mm:ss"` in UTC.
- Respect the 1000 requests/minute per-IP limit; back off on HTTP 429.
- Error semantics: see `errors/total-expert-problem-types.yml`; conventions: `conventions/total-expert-conventions.yml`.
