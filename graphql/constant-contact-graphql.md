# Constant Contact GraphQL Schema

This directory contains a conceptual GraphQL schema for the Constant Contact V3 API. Constant Contact is a small-business email and digital marketing platform offering email campaigns, automation, SMS, contact management, surveys, and events. The schema is derived from the public Constant Contact REST API v3 (`api.cc.email/v3`) and its developer documentation at `developer.constantcontact.com`.

## Source API

- **Base URL:** `https://api.cc.email/v3`
- **Auth:** OAuth 2.0 (Authorization Code, PKCE, and JWT-bearer flows)
- **Rate limits:** 4 requests/second, 10,000 requests/day per application
- **Developer portal:** `https://developer.constantcontact.com/`
- **OpenAPI spec:** `https://api.cc.email/v3/swagger.yaml`

## Schema File

`constant-contact-schema.graphql`

## Domain Coverage

The schema covers the full surface area of the Constant Contact V3 REST API organized into the following domains:

### Account & Organization
Types representing an account holder, their organization address, subscription plan, billing information, and current usage metrics.

Types: `Account`, `AccountSummary`, `OrganizationAddress`, `Plan`, `BillingInfo`, `Usage`

### Auth & API Keys
Types for OAuth2 tokens and registered API key applications.

Types: `APIKey`, `Token`

### Webhooks
Webhook subscriptions for contact and campaign lifecycle events.

Types: `Webhook`

### Contact Management
The core contact record with all associated sub-objects: phone numbers, street addresses, list memberships, tags, custom field values, notes, and SMS consent state.

Types: `Contact`, `ContactDetail`, `ContactPhone`, `ContactAddress`, `Note`, `Tag`, `SMSMA`, `PhoneList`

### Contact Lists
Named groupings of contacts used as campaign send targets.

Types: `EmailList`, `ContactList`

### Custom Fields
User-defined metadata fields attached to contacts.

Types: `CustomField`

### Segments
Dynamic contact groups built from criteria such as field values, list membership, and engagement history.

Types: `Segment`

### Email Campaigns
Campaign records, per-activity content and send configuration, and scheduled sends.

Types: `EmailCampaign`, `CampaignActivity`, `ScheduledActivity`, `CampaignSummary`

### Email Reporting
Per-contact engagement data and aggregate link-click reporting.

Types: `EmailActivity`, `CampaignActivityEngagement`, `EmailOpen`, `LinkClicks`, `ClickDetail`, `UnsubscribedContact`, `BouncedContact`

### Bulk Activities
Asynchronous high-volume operations for importing, exporting, and mutating large contact sets.

Types: `BulkActivity`, `BulkActivityError`, `BulkActivityLinks`

### Domain Authentication
DKIM and SPF records for sender domain verification.

Types: `DomainAuthentication`, `DKIMRecord`

### Events
Event marketing and registration management including sessions, tracks, and check-in.

Types: `Event`, `EventLocation`, `EventSummary`, `EventDetail`, `EventTrack`, `EventSession`, `EventActivity`

### Tickets & Orders
Ticket types, purchase orders, line items, payment records, and discount codes.

Types: `TicketType`, `Order`, `OrderItem`, `Payment`, `Discount`

### Event Registration
Registrant profiles, individual registrations, check-in state, and form answers.

Types: `Registrant`, `Registration`, `RegistrationAnswer`, `EventRegistration`

### Forms & Landing Pages
Web capture forms, their fields and links, published landing pages, and web signup widgets.

Types: `Form`, `FormField`, `FormLink`, `LandingPage`, `WebSignup`

### Integrations
Third-party platform integrations (Shopify, WooCommerce, etc.).

Types: `Integration`

## Named Types Summary

| Category | Types |
|---|---|
| Enums | `CampaignStatus`, `ActivityStatus`, `ContactStatus`, `BulkActivityType`, `PhoneType`, `AddressType`, `CustomFieldType`, `SegmentCriteriaType`, `WebhookEventType`, `PaymentStatus`, `TicketStatus`, `EventStatus`, `PlanType`, `FormFieldType`, `BounceCategory`, `SMSStatus` |
| Account | `Account`, `AccountSummary`, `OrganizationAddress`, `Plan`, `BillingInfo`, `Usage` |
| Auth | `APIKey`, `Token` |
| Webhooks | `Webhook` |
| Contacts | `Contact`, `ContactDetail`, `ContactPhone`, `ContactAddress`, `Note`, `Tag`, `SMSMA`, `PhoneList` |
| Lists | `EmailList`, `ContactList` |
| Custom Fields | `CustomField` |
| Segments | `Segment` |
| Campaigns | `EmailCampaign`, `CampaignActivity`, `ScheduledActivity`, `CampaignSummary` |
| Reporting | `EmailActivity`, `CampaignActivityEngagement`, `EmailOpen`, `LinkClicks`, `ClickDetail`, `UnsubscribedContact`, `BouncedContact` |
| Bulk | `BulkActivity`, `BulkActivityError`, `BulkActivityLinks` |
| Domain Auth | `DomainAuthentication`, `DKIMRecord` |
| Events | `Event`, `EventLocation`, `EventSummary`, `EventDetail`, `EventTrack`, `EventSession`, `EventActivity` |
| Tickets & Orders | `TicketType`, `Order`, `OrderItem`, `Payment`, `Discount` |
| Registration | `Registrant`, `Registration`, `RegistrationAnswer`, `EventRegistration` |
| Forms & Pages | `Form`, `FormField`, `FormLink`, `LandingPage`, `WebSignup` |
| Integrations | `Integration` |
| Connections | `ContactConnection`, `ContactEdge`, `EmailListConnection`, `EmailListEdge`, `TagConnection`, `TagEdge`, `SegmentConnection`, `SegmentEdge`, `EmailCampaignConnection`, `EmailCampaignEdge`, `EmailOpenConnection`, `EmailOpenEdge`, `UnsubscribedContactConnection`, `UnsubscribedContactEdge`, `BouncedContactConnection`, `BouncedContactEdge`, `BulkActivityConnection`, `BulkActivityEdge`, `EventConnection`, `EventEdge`, `EventRegistrationConnection`, `EventRegistrationEdge`, `FormConnection`, `FormEdge`, `LandingPageConnection`, `LandingPageEdge`, `WebSignupConnection`, `WebSignupEdge`, `PageInfo` |
| Root | `Query`, `Mutation` |
| Inputs | `CreateContactInput`, `UpdateContactInput`, `ContactPhoneInput`, `ContactAddressInput`, `ContactDetailInput`, `BulkActivityInput`, `BulkActivitySourceInput`, `CreateEventInput`, `UpdateEventInput`, `EventLocationInput`, `CreateTicketTypeInput`, `UpdateTicketTypeInput`, `CreateDiscountInput` |

## Design Notes

- All IDs are typed as `ID!` following GraphQL convention.
- DateTime fields use the `DateTime` scalar (ISO-8601).
- Complex or provider-specific filter criteria that vary per endpoint use the `JSON` scalar.
- Pagination follows the Relay cursor connection pattern throughout.
- This is a conceptual schema — Constant Contact does not currently expose a native GraphQL endpoint. This schema represents what a GraphQL layer over the v3 REST API would look like.
