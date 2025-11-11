# Integration architecture — Segment → PostHog → Customer.io

## Overview
1. Server → Segment (Data user segmentation using SQL)
2. Segment routes event stream to PostHog (analytics destination) and Customer.io (identify/profile updates + selected events for campaigns)
3. PostHog is the product analytics datastore used for cohorting, funnels, and product analysis.
4. Customer.io is used for email/message campaigns triggered by events or user properties.

## Data flow
- Identify/alias call to Segment sets user profile (user_id, email_hash, plan, country).
- Segment track events forwarded to PostHog in near real-time.
- For messaging, forward 'Email Clicked', 'Signup Completed', or 'Purchase Completed' events to Customer.io; Customer.io receives Identify calls to keep profile attributes current.
- Export: PostHog cohorts can be exported to Customer.io (via Segment) to run campaigns.

## Assumptions
- Email is hashed before being sent to public dataset (no raw PII).
- Rate-limiting considered—only critical events forwarded to Customer.io to control cost.
