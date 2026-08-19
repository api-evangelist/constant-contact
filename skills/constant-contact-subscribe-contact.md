---
name: constant-contact-subscribe-contact
description: Add or update a contact in Constant Contact and put them on one or more lists, using the opt-in sign-up path.
generated: '2026-08-13'
method: generated
source: openapi/constant-contact-contacts-api-openapi.yml, openapi/constant-contact-contact-lists-api-openapi.yml
api: Constant Contact V3 API
base_url: https://api.cc.email/v3
scopes:
  - contact_data
operations:
  - getLists
  - createOrUpdateContact
  - getContacts
  - getContact
  - createContact
  - putContact
---

# Subscribe a contact

Adds a person to a Constant Contact account and places them on one or more lists. Prefer the
sign-up-form path (`createOrUpdateContact`) over a raw create — it is the endpoint Constant
Contact intends for opt-in, it is an upsert, and it will not fail when the address already
exists.

## Prerequisites

- An OAuth2 access token with the `contact_data` scope. Token TTL is 24 hours; refresh needs
  `offline_access`.
- `Authorization: Bearer <access_token>` and `Content-Type: application/json` on every request.

## Steps

### 1. Resolve the target list ids

```
GET /contact_lists?limit=500&include_count=true
```
`getLists` returns `lists[]` with `list_id`, `name` and `membership_count`. Match on `name` and
keep the `list_id`. Do not cache list ids across accounts — they are per-account UUIDs.

### 2. Upsert the contact

```
POST /contacts/sign_up_form
```
`createOrUpdateContact` takes `ContactCreateOrUpdateInput`. **`list_memberships` is the only
required field** — a contact must land on at least one list.

```json
{
  "email_address": "person@example.com",
  "first_name": "Ada",
  "last_name": "Lovelace",
  "list_memberships": ["<list_id>"],
  "custom_fields": [{"custom_field_id": "<custom_field_id>", "value": "..."}]
}
```

Returns `201` with `contact_id` and an `action` field telling you whether the contact was
created or updated. This endpoint records the contact as opting in via a sign-up form, which is
the consent posture you want for marketing sends.

### 3. Verify (optional)

```
GET /contacts?email=person@example.com&include=list_memberships,custom_fields,taggings
```

## Rules and gotchas

- **`putContact` is a full replace, not a patch.** `PUT /contacts/{contact_id}` clears any field
  you omit. If you only have partial data, read the contact first with `getContact` and send the
  merged document back. Use `createOrUpdateContact` instead wherever you can.
- **An SMS-only contact is legal.** Since May 2025 a contact can exist with an `sms_channel` and
  no `email_address`. Do not assume `email_address` is present when reading.
- **Custom fields are referenced by id, not name.** Call `getCustomFields` once and map
  label → `custom_field_id`; sending an unknown id returns `400` with
  `error_key: contacts.api.validation.error`.
- **Rate limit: 4 requests/second and 10,000/day per application, with no headers to tell you
  where you are.** For more than a handful of contacts, do not loop this skill — use
  `constant-contact-bulk-import-contacts` instead.
- **No idempotency key exists.** A retried `POST /contacts` after a timeout can produce a `409`
  Conflict rather than returning the original resource. Prefer the upsert endpoint, which is
  naturally idempotent on `email_address`.
- **Errors** come back as `[{"error_key": "...", "error_message": "..."}]` — usually an array,
  sometimes a bare object on auth failures. See `errors/constant-contact-problem-types.yml`.
- A `403` means one of three different things: missing scope, missing user privilege, or a
  deactivated application. Call `getUserPrivileges` to tell them apart.
