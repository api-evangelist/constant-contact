---
name: constant-contact-send-email-campaign
description: Create an email campaign in Constant Contact, set its content and audience, test it, and schedule the send.
generated: '2026-08-13'
method: generated
source: openapi/constant-contact-email-campaigns-api-openapi.yml, openapi/constant-contact-email-scheduling-api-openapi.yml
api: Constant Contact V3 API
base_url: https://api.cc.email/v3
scopes:
  - campaign_data
  - contact_data
operations:
  - createEmailCampaignUsingPOST
  - retrieveEmailCampaignUsingGET
  - retrieveEmailCampaignActivityUsingGET
  - updateEmailCampaignActivityUsingPUT
  - retrieveEmailCampaignActivityPreviewUsingGET
  - testSendCampaignActivityUsingPOST
  - scheduleEmailCampaignActivityUsingPOST
  - unscheduleEmailCampaignActivityUsingDELETE
  - retrieveEmailSendHistoryUsingGET
---

# Send an email campaign

## The one thing to understand first

A **campaign** (`campaign_id`) is a shell. All the content, the audience and every report hang
off a **campaign activity** (`campaign_activity_id`). If you key your workflow on `campaign_id`
you will not find the HTML, the recipients, or the stats. Always carry the
`campaign_activity_id` of the activity whose `role` is `primary_email`.

## Prerequisites

- Access token with `campaign_data` (and `contact_data` if you are resolving lists or segments).
- The account must have a verified from-address — read the allowed set with
  `retrieveEmailAddresses` (`GET /account/emails`) before you set `from_email`.

## Steps

### 1. Create the campaign

```
POST /emails
```
`createEmailCampaignUsingPOST` takes `EmailCampaignComplete`, which requires exactly two fields:

```json
{
  "name": "August newsletter",
  "email_campaign_activities": [
    {
      "format_type": 5,
      "from_email": "news@example.com",
      "from_name": "Example",
      "reply_to_email": "news@example.com",
      "subject": "August newsletter",
      "html_content": "<html>...</html>",
      "physical_address_in_footer": { "address_line1": "...", "city": "...", "state_code": "...", "postal_code": "...", "country_code": "US" }
    }
  ]
}
```

The response carries `campaign_id` and `campaign_activities[]`. **Save the
`campaign_activity_id` where `role` is `primary_email`.**

`physical_address_in_footer` is not decoration — it is the CAN-SPAM postal address. Omit it and
the account's own address is used; supply a wrong one and you have a compliance problem, not an
API problem.

### 2. Set the audience

```
PUT /emails/activities/{campaign_activity_id}
```
`updateEmailCampaignActivityUsingPUT` is where `contact_list_ids[]` and `segment_ids[]` are set,
alongside subject and content. This is a **full replace** of the activity — read it first with
`retrieveEmailCampaignActivityUsingGET` and send the merged document back, or you will blank the
fields you left out.

### 3. Preview and test

```
GET  /emails/activities/{campaign_activity_id}/previews
POST /emails/activities/{campaign_activity_id}/tests
```
`retrieveEmailCampaignActivityPreviewUsingGET` renders the merged HTML.
`testSendCampaignActivityUsingPOST` sends a real test to named recipients. Do both before
step 4 — there is no draft/publish gate and no undo once a send starts.

### 4. Schedule the send

```
POST /emails/activities/{campaign_activity_id}/schedules
```
`scheduleEmailCampaignActivityUsingPOST` takes `EmailScheduleInput`, whose only required field
is `scheduled_date`:

```json
{ "scheduled_date": "2026-09-01T14:00:00.000Z" }
```

**`"scheduled_date": "0"` means send immediately.** That string is the difference between a
scheduled campaign and an irreversible blast. An agent must never pass `"0"` without explicit
human confirmation.

### 5. Cancel or confirm

```
DELETE /emails/activities/{campaign_activity_id}/schedules   # unschedule, before the send starts
GET    /emails/activities/{campaign_activity_id}/send_history
```

## Rules and gotchas

- `retrieveEmailCampaignUsingGET` is the only way back from a `campaign_id` to its activities.
- A/B tests attach to the **activity**: `createEmailCampaignABTestUsingPOST` on
  `/emails/activities/{campaign_activity_id}/abtest`.
- `renameEmailCampaignUsingPATCH` is the one true PATCH on this API — everything else that
  updates is a PUT full-replace.
- Rate limit is 4/sec and 10,000/day per application with no `Retry-After` and no
  `X-RateLimit-*` headers; a `429` carrying `error_key: quota_exceeded` will not clear until
  00:00 UTC, so do not treat it as a throttle and retry.
- There are **no send/open/click webhooks**. Post-send measurement is polling only — see
  `constant-contact-report-campaign-performance`.
- No idempotency key: a retried `POST /emails` creates a second campaign.
