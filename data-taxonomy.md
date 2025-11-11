# Data Taxonomy — Product Analytics

## Assumptions
- All timestamps: ISO 8601 UTC
- Session boundary: 30 minutes inactivity.
- User identifiers are hashed (no PII).
- Events follow Segment-spec: { user_id, event, properties, timestamp, context, anonymous_id }.

---

## Entities

### User
- user_id (string, hashed) — Unique user identifier. (required)
- created_at (timestamp, ISO8601 UTC) — User signup time.
- email_hash (string) — SHA256 hashed email (no raw email).
- company (string) — company name (optional).
- company_size (integer) — company employee count (optional).
- plan (string) — e.g., trial, free, pro, enterprise.
- country (string) — ISO 3166-1 alpha-2 (e.g., ID, SG, US).
- signup_source (string) — web, partner, referral.
- is_admin (boolean) — indicates admin user.
- last_seen_at (timestamp) — last event timestamp.

### Events
- event_id (string) — unique event id (uuid).
- event (string) — event name (see Event list).
- user_id (string) — hashed user id.
- timestamp (timestamp ISO8601 UTC).
- properties (json) — event-specific props.
- context (json) — device, browser, ip (no raw IP in public dataset).
- session_id (string) — derived session id computed by frontend or ingestion.

---

## Event Description
- Page Viewed : user viewed page (properties: page_url, page_title)
- Signup Completed : user completed signup (properties: plan)
- Logged In : user signed in (properties: method)
- Product Viewed : user opened product page (product_id, category)
- Segment Created : user created a segment in UI (segment_id, segment_rules)
- Segment Applied : user ran a segment (segment_id, result_count)
- Cohort Exported : user exported cohort (format)
- Feature Used : feature-specific usage (feature_name)
- Checkout Initiated : beginning of purchase flow (cart_value)
- Purchase Completed : successful purchase (order_id, amount, currency)
- Email Clicked : click from Customer.io email (campaign_id)
- Churn Warning Triggered : in-app signal (reason)
