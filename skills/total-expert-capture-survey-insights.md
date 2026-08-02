---
name: Capture surveys and insights for a contact
description: Store custom data against Total Expert contacts using surveys, and trigger marketing automation with insights.
api: openapi/total-expert-openapi.yml
operations: [createContactSurvey, getContactSurveys, createContactSurveyResponseAsUser, createContactAsAdmin, createInsightAsAdmin, fetchInsightTypes]
generated: '2026-07-21'
method: generated
---

# Capture surveys and insights for a contact

Surveys hold structured extra data (fields the standard contact object lacks); insights signal events from your platform that can drive Total Expert journeys and marketing automation.

## Surveys
1. Create the survey structure once: POST `/v1/contact-surveys` (`createContactSurvey`) with the questions, potential answers, and field types. The response returns the survey ID.
2. Retrieve the generated question IDs with GET `/v1/contact-surveys` (`getContactSurveys`).
3. Send the contact first (POST `/v1/contacts`, `createContactAsAdmin`) in a separate request to obtain the TE contact ID — survey responses must reference an existing contact.
4. POST `/v1/contact-survey-responses` (`createContactSurveyResponseAsUser`) tying question IDs to answers for that contact. New submissions accumulate in the UI rather than overwriting old data, and can trigger marketing automation or journey conditions.

## Insights
5. List available types with GET `/v1/insight-types` (`fetchInsightTypes`).
6. POST `/v1/insights` (`createInsightAsAdmin`) to record an event (e.g. "Mobile App Downloaded") against a contact.

## Rules
- Prefer surveys over custom fields for integration data: custom fields must be created by Total Expert per customer and cannot be edited in the UI.
- Auth, pagination, dedup, and rate-limit rules are as in `conventions/total-expert-conventions.yml`.
