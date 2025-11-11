# Segmentation Logic — 5 Segments (3 behavioral, 2 demographic)

**Assumptions:** Data loaded into BigQuery table. Event properties JSON is in `properties` column. Sessions boundary = 30 minutes. Timestamps are `timestamp` (ISO).

---

## Segment 1 — Power Users (behavioral)
**Case**: Users who performed ≥ 20 product-related events in the last 7 days.  
**Rationale**: Highly active users likely to trial new features.

**SQL**:
```sql
WITH last7 AS (
  SELECT user_id, COUNTIF(event IN ('Product Viewed','Feature Used','Segment Created','Segment Applied')) AS activity_count
  FROM `project.dataset.sample_events`
  WHERE timestamp >= TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 7 DAY)
  GROUP BY user_id
)
SELECT user_id
FROM last7
WHERE activity_count >= 20;
```

**PostHog cohort**: users with event count (Product Viewed OR Feature Used OR Segment Created OR Segment Applied) >= 20 in last 7 days.

---

## Segment 2 — Recently Returned (behavioral)
**Case**: Users who were inactive for 30–60 days, then triggered at least 1 event in last 7 days (reactivations).  
**Rationale**: Good for win-back messaging.

**SQL**:
```sql
WITH last_event AS (
  SELECT user_id, MAX(timestamp) AS last_ts
  FROM `project.dataset.sample_events`
  GROUP BY user_id
)
SELECT DISTINCT e.user_id
FROM `project.dataset.sample_events` e
JOIN last_event le ON e.user_id = le.user_id
WHERE e.timestamp >= TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 7 DAY)
  AND EXISTS (
    SELECT 1 FROM `project.dataset.sample_events` e2
    WHERE e2.user_id = e.user_id
      AND e2.timestamp < TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 30 DAY)
  );
```

**PostHog cohort**: users with last event 0–7 days AND previous event >30 days ago.

---

## Segment 3 — High Value Converters (behavioral)
**Case**: Users who completed at least one `Purchase Completed` with amount >= 150 USD in last 90 days.  
**Rationale**: Target for upsell and retention.

**SQL**:
```sql
SELECT DISTINCT user_id
FROM `project.dataset.sample_events`
WHERE event = 'Purchase Completed'
  AND SAFE_CAST(JSON_EXTRACT_SCALAR(properties,'$.amount') AS FLOAT64) >= 150
  AND timestamp >= TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 90 DAY);
```

**PostHog cohort**: event = Purchase Completed with amount >= 150 in last 90 days.

---

## Segment 4 — Enterprise Customers (demographic)
**Case**: Users whose `company_size` >= 200 or plan = 'enterprise'.  
**Rationale**: For account management and feature rollout.

**SQL**:
```sql
SELECT user_id
FROM `project.dataset.sample_users`
WHERE company_size >= 200 OR plan = 'enterprise';
```

**PostHog cohort**: person property `company_size >= 200 OR plan = enterprise`.

---

## Segment 5 — Indonesia SMB (demographic)
**Case**: Users in Indonesia (country = 'ID') with company_size between 2 and 100 and plan in (trial, free).  
**Rationale**: Localization and market-specific campaigns.

**SQL**:
```sql
SELECT user_id
FROM `project.dataset.sample_users`
WHERE country = 'ID'
  AND company_size BETWEEN 2 AND 100
  AND plan IN ('free','trial');
```

**PostHog cohort**: person property `country = ID AND company_size >=2 AND company_size <= 100 AND plan in (free,trial)`.
