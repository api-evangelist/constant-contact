---
name: constant-contact-report-campaign-performance
description: Pull open, click, bounce, forward and opt-out performance for Constant Contact email and SMS campaigns, and per-contact engagement history.
generated: '2026-08-13'
method: generated
source: openapi/constant-contact-email-reporting-api-openapi.yml, openapi/constant-contact-contacts-reporting-api-openapi.yml, openapi/constant-contact-sms-reporting-api-openapi.yml
api: Constant Contact V3 API
base_url: https://api.cc.email/v3
scopes:
  - campaign_data
  - contact_data
operations:
  - getAllBulkEmailCampaignSummaries
  - getEmailSummary
  - getEmailCampaignActivitySummary
  - getCampaignActivityLinkStats
  - getSends
  - getOpens
  - getUniqueOpens
  - getDidNotOpens
  - getClicks
  - getForwards
  - getOptouts
  - getBounces
  - getContactTracking
  - getContactOpenClickRate
  - getContactTrackingCount
  - getAllBulkSmsCampaignSummaries
---

# Report on campaign performance

Constant Contact publishes **no engagement webhooks**. Sends, opens, clicks, bounces and
opt-outs are poll-only, and every poll spends part of the same 10,000 requests/day the writes
use. Design the pull top-down: summaries first, per-contact detail only where you need it.

## Prerequisites

- Access token with `campaign_data`; `contact_data` as well for the per-contact reports.
- The `campaign_activity_id` — **not** the `campaign_id`. Every email reporting path is keyed on
  the activity. Get it from `retrieveEmailCampaignUsingGET`.

## Tier 1 — account-wide summaries (cheapest)

```
GET /reports/summary_reports/email_campaign_summaries?limit=50
GET /reports/summary_reports/sms_campaign_summaries?limit=50
```
`getAllBulkEmailCampaignSummaries` and `getAllBulkSmsCampaignSummaries` return counts and
percentages for many campaigns in one call. Start here. Both are cursor-paged — follow
`_links.next.href` and remember it is **path-relative**, so prepend `https://api.cc.email`.

## Tier 2 — stats for named campaigns

```
GET /reports/stats/email_campaigns/{campaign_ids}
GET /reports/stats/email_campaign_activities/{campaign_activity_ids}
```
`getEmailSummary` and `getEmailCampaignActivitySummary` accept a **comma-separated list of ids in
the path**, so a batch of campaigns costs one request. Use this rather than one call per
campaign.

These are the two endpoints that use the `StatsError` shape — a bare
`{"error_key": "reportingsvc...", "error_message": "..."}` object, not the array envelope the
rest of the API returns.

## Tier 3 — per-link and per-contact detail (expensive)

```
GET /reports/email_reports/{campaign_activity_id}/links
GET /reports/email_reports/{campaign_activity_id}/tracking/{opens|unique_opens|clicks|sends|bounces|forwards|optouts|didnotopens}
```
`getCampaignActivityLinkStats` gives click counts per link URL (duplicate URLs are combined by
default). The `tracking/*` family returns individual activity rows, cursor-paged with `limit`
(default 50, max typically 500).

Useful filters on the tracking endpoints: `bounce_code` on `getBounces`, `url_id` and
`no_clicks` on `getClicks`, `include_campaign_activity_names` to avoid a second lookup.

## Per-contact engagement

```
GET /reports/contact_reports/{contact_id}/activity_summary
GET /reports/contact_reports/{contact_id}/open_and_click_rates
GET /reports/contact_reports/{contact_id}/activity_details?limit=100
```
`getContactTrackingCount`, `getContactOpenClickRate` and `getContactTracking`. The detail
endpoint caps `limit` at 100 — lower than the 500 most collections allow.

## Rules and gotchas

- **Budget the poll.** 4 requests/second and 10,000/day per application, shared across every
  client account a partner integration serves, with no `X-RateLimit-*` and no `Retry-After`.
  Branch on `error_key`: `throttled` clears within the second, `quota_exceeded` does not clear
  until 00:00 UTC.
- **Report freshness is not documented.** No endpoint returns a "stats as of" timestamp, so an
  agent cannot tell a genuinely zero-open campaign from one whose stats have not aggregated yet.
  Do not report a zero as a result within the first hours after a send.
- Reporting windows use `start` / `end` (ISO-8601) on the endpoints that accept them.
- `getDidNotOpens` is a real endpoint — use it instead of diffing sends against opens yourself.
- Landing-page reporting lives on its own paths under
  `/reports/landing_pages/campaign_details/{campaign_activity_id}/…` and is also keyed on the
  activity id.
