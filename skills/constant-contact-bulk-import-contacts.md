---
name: constant-contact-bulk-import-contacts
description: Import, export or bulk-modify contacts in Constant Contact through the asynchronous activities API, and poll the job to completion.
generated: '2026-08-13'
method: generated
source: openapi/constant-contact-bulk-activities-api-openapi.yml
api: Constant Contact V3 API
base_url: https://api.cc.email/v3
scopes:
  - contact_data
operations:
  - bulkImportContactsJSON
  - contactsCSVImport
  - postContactsExport
  - getContactsExport
  - getActivity
  - getActivityStatusCollection
  - postContactDelete
  - postListAddContact
  - postListRemoveContact
  - postTagAddContact
  - postTagRemoveContact
---

# Bulk contact operations

Every bulk mutation on this API is **asynchronous**. You POST a job, you get an `activity_id`,
and you poll. Nothing about the response to the POST tells you whether the data was accepted —
only the activity does.

Use this skill instead of looping the single-contact endpoints any time you have more than a
few dozen records: at 4 requests/second, 10,000 contacts one-at-a-time is over 40 minutes of
wall clock and consumes the entire daily quota.

## Prerequisites

- Access token with `contact_data`.
- Target `list_ids` resolved via `getLists`.

## Import from JSON

```
POST /activities/contacts_json_import
```
`bulkImportContactsJSON` takes `ContactsJsonImport`. Both `import_data` and `list_ids` are
required — an import must land on at least one list.

```json
{
  "import_data": [
    { "email": "a@example.com", "first_name": "Ada", "custom_fields": [...] }
  ],
  "list_ids": ["<list_id>"]
}
```

Returns `202` with `activity_id` and `_links.self.href`.

## Import from CSV

```
POST /activities/contacts_file_import
```
`contactsCSVImport` is the exception to every content-type rule on this API: **`Content-Type`
must be left UNSPECIFIED** and the upload must be a `.csv` file. Setting
`Content-Type: application/json` here returns `415`.

## Export

```
POST /activities/contact_exports      -> activity_id
GET  /activities/{activity_id}        -> poll until state is completed
GET  /contact_exports/{file_export_id} -> download
```
`postContactsExport` starts the job; when the activity completes, follow
`_links.results.href` — it points at `getContactsExport`.

## Other bulk jobs

Same POST-then-poll shape:

| Job | Operation | Path |
|---|---|---|
| Delete contacts | `postContactDelete` | `/activities/contact_delete` |
| Add to lists | `postListAddContact` | `/activities/add_list_memberships` |
| Remove from lists | `postListRemoveContact` | `/activities/remove_list_memberships` |
| Delete lists | `postListDelete` | `/activities/list_delete` |
| Tag contacts | `postTagAddContact` | `/activities/contacts_taggings_add` |
| Untag contacts | `postTagRemoveContact` | `/activities/contacts_taggings_remove` |
| Delete tags | `postTagDelete` | `/activities/contacts_tags_delete` |
| Delete custom fields | `postCustomFieldDelete` | `/activities/custom_fields_delete` |

## Poll the activity

```
GET /activities/{activity_id}
```
`getActivity` returns the job state plus an `ActivityStatus` counter block:

- `items_total_count`, `items_completed_count` — progress
- `person_count`, `list_count` — what was affected
- `error_count`, `correctable_count`, `cannot_add_to_list_count` — what failed

Poll on an interval, not a tight loop; every poll spends one of your 10,000 daily requests.
`getActivityStatusCollection` (`GET /activities?limit=500`) lists recent activities if you lost
an `activity_id`.

## Rules and gotchas

- **The queue is capped at 1,000 concurrent activities per user account.** Exceeding it returns
  `429` with `error_message: "You exceeded 1,000 queued activities for this user account."` —
  a different 429 from the request-rate ones, and it will not clear by backing off on request
  rate. Drain the queue.
- **Per-record failures do not fail the job.** A completed activity with a non-zero `error_count`
  is a partial success. Always read the counters; never treat `completed` as `all rows landed`.
- Bulk errors use a **third** error shape — `{"message": "..."}` inside the activity status, not
  the `{error_key, error_message}` envelope the rest of the API uses.
- **No idempotency key.** A retried import POST enqueues a second job over the same data. If a
  POST times out, call `getActivityStatusCollection` and look for the job before resubmitting.
- `postContactDelete` is destructive and irreversible from the API. Treat it as
  human-in-the-loop; `agentic-access/constant-contact-agentic-access.yml` classifies the
  destructive operations on this API.
