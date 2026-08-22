# Constant Contact (constant-contact)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Constant Contact is a small-business email and digital marketing platform offering email campaigns, automation, SMS, contact management, surveys, and events. The Constant Contact V3 API is a REST + JSON, OAuth2-protected service published at api.cc.email/v3 covering accounts, contacts, lists, tags, custom fields, segments, email campaigns, A/B tests, schedules and tests, bulk activities (CSV/JSON import, export, list and tag mutations), events with registration and check-in, reporting, and partner provisioning.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/constant-contact/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/constant-contact/refs/heads/main/apis.yml)

## Scope

- **Type:** Index
- **Position:** Consumer
- **Access:** 3rd-Party

## Tags

- Campaigns
- Contacts
- Email Marketing
- Events
- Reporting
- SMS
- Surveys

## Timestamps

- **Created:** 2025-03-01
- **Modified:** 2026-05-19

## APIs

### Constant Contact V3 API

Production REST API for Constant Contact's email marketing, SMS, and events platform. OAuth2 authorization (auth code, PKCE, and JWT-bearer flows) gates all endpoints across account services, contacts, contact lists, tags, custom fields, segments, email campaigns and activities (with A/B tests, schedules, and tests), bulk activities for high-volume mutations, events (registrations, tracks, check-in, payment status), contact reporting, landing pages, and partner technology-account provisioning.

- **Human URL:** [https://developer.constantcontact.com/](https://developer.constantcontact.com/)
- **Base URL:** `https://api.cc.email/v3`

#### Tags

- Campaigns
- Contacts
- Email Marketing
- Events
- OAuth2
- REST
- SMS

#### Properties

- [Documentation](https://developer.constantcontact.com/)
- [Getting Started](https://developer.constantcontact.com/api_guide/index.html)
- [Authentication](https://developer.constantcontact.com/api_guide/auth_overview.html)
- [OpenAPI](https://api.cc.email/v3/swagger.yaml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [OpenAPI](openapi/constant-contact-v3-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/constant-contact-v3.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/constant-contact-v3.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [LinkedIn](https://www.linkedin.com/company/constant-contact)
- [Website](https://www.constantcontact.com/)
- [Portal](https://developer.constantcontact.com/)
- [Getting Started](https://developer.constantcontact.com/api_guide/index.html)
- [Authentication](https://developer.constantcontact.com/api_guide/auth_overview.html)
- [API Reference](https://developer.constantcontact.com/api_reference/index.html)
- [OpenAPI](https://api.cc.email/v3/swagger.yaml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Status Page](https://status.constantcontact.com/)
- [Support](https://www.constantcontact.com/help)
- [Community](https://community.constantcontact.com/)
- [Blog](https://blogs.constantcontact.com/)
- [GitHub Organization](https://github.com/constantcontact)
- [Privacy Policy](https://www.constantcontact.com/legal/privacy-statement)
- [Terms of Service](https://www.constantcontact.com/legal/terms-of-use)
- [JSON-LD](json-ld/constant-contact-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [JSON Schema](json-schema/constant-contact-contact-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/constant-contact-campaign-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [Spectral Rules](rules/constant-contact-rules.yml) — [Spectral](https://docs.stoplight.io/docs/spectral)
- [Features](undefined)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
