# Data Governance (PDPA Singapore + PDP Indonesia)

## Principles applied
- Data minimization: only collect attributes necessary for analytics and segmentation (no raw emails or government IDs in public datasets).
- Purpose limitation: events and user properties are only used for product analytics, segmentation, and messaging.
- Consent & lawful basis: for production, ensure explicit consent for tracking where required. For SG & ID, follow local guidelines and client contract.
- Retention: store raw events for N days (e.g., 365 days) and aggregated metrics longer; user PII retention policy configurable (e.g., 90 days for raw email hashed).
- Access control: least privilege for analytics datasets; separate dev/test environments.
- Pseudonymization: hash all emails and remove IPs before publishing. Use salted SHA256 for hashing.
- Data subject rights: define process for access, correction, and deletion requests; pipeline must support user deletion (scrub/redact).
- Cross-border transfer: document where data is stored (GCP region); ensure client is aware for compliance.
- Logging & audit: store change logs for schema and segmentation changes; maintain README and change log.

## Implementation notes
- Customer.io: only send hashed identifier + minimal profile attributes required for messaging.
- PostHog: disable session recording by default; avoid capturing raw fields with PII.
