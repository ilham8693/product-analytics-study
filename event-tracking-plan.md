# Event Tracking Plan (Segment + PostHog guidance)

## General guidance
- Track events from client side with server-side using Segment HTTP API.
- Each event must include: user_id (hashed), timestamp (ISO8601 UTC), event name, properties JSON, context, session_id.
- Use stable property names (snake_case) and avoid nested fields unless necessary.

## Example event definitions

### 1) Signup Completed
- Segment track name: "Signup Completed"
- Required properties:
  - plan (string) — "trial"|"free"|"pro"|"enterprise"
  - referral (string|null) — source tag
- Example:
```
analytics.track('Signup Completed', {
  plan: 'trial',
  referral: 'google_ads'
}, { userId: 'u_abc123' })
```

### 2) Product Viewed
- Segment track name: "Product Viewed"
- Required properties:
  - product_id (string)
  - category (string)
  - page_url (string)
- Example:
```
analytics.track('Product Viewed', {
  product_id: 'prod_123',
  category: 'analytics',
  page_url: '/products/analytics'
}, { userId: 'u_abc123' })
```

### 3) Segment Created
- Segment track name: "Segment Created"
- Required properties:
  - segment_id (string)
  - segment_rules (string / JSON) — simplified rule string or JSON expression
  - created_via (string) — "ui"|"api"
- Example:
```
analytics.track('Segment Created', {
  segment_id: 'seg_001',
  segment_rules: '{"event":"Purchase Completed","min_amount":100}',
  created_via: 'ui'
}, { userId: 'u_abc123' })
```

### 4) Purchase Completed
- Segment track name: "Purchase Completed"
- Required props:
  - order_id (string)
  - amount (float)
  - currency (string)
  - items_count (integer)
- Example:
```
analytics.track('Purchase Completed', {
  order_id: 'o_123',
  amount: 199.00,
  currency: 'USD',
  items_count: 2
}, { userId: 'u_abc123' })
```

## Mapping to PostHog
- Create event names in PostHog identical to Segment track names.
- Map key properties to event properties in PostHog; for cohorting ensure numeric properties are stored as numeric types.
- Use Person/Property mapping in PostHog to store user profile attributes (plan, company_size, country).
