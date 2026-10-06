# PF-001: Platform Foundations

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Core foundation built |
| Last reviewed | 6 October 2026 |

## PF-001A: Organisation and Branch tenancy

Surplus to Supper is the parent Organisation. Woking, Kingston, Sunbury, and future hubs are Branches. Operational records are Branch-owned unless explicitly defined as Organisation-wide.

Acceptance criteria:

- An operational API cannot read or mutate another Branch's records.
- Existing prototype data is retained under Surplus to Supper -> Woking.
- Organisation-wide category definitions can have Branch-specific activation and average weights.
- Adding another Branch does not require redesigning operational tables.

## PF-001B: Google identity and permissions

Google is the only sign-in provider. A first-time verified Google identity automatically receives Woking Volunteer membership. Existing users whose account or membership is inactive remain blocked.

Acceptance criteria:

- Organisation Admins and Branch Managers see operational screens and Admin.
- Volunteers see Intake, Inventory, Redistribution, and Dashboard, but not Admin.
- Server APIs enforce the same permissions as the screens.
- Management can change roles and deactivate Google email addresses.

## PF-001C: Audit history

Material changes create an append-only audit event containing Branch, actor, action, entity, timestamp, and before/after data where applicable.

Acceptance criteria:

- Donor, category weight, delivery, delivery item, submission, access, and CSV changes are audited.
- Management can view the latest Branch audit events.
- Application users cannot edit or delete audit events.

## PF-001D: CSV data exchange

CSV import and export are mandatory for migration and operational collaboration.

Acceptance criteria:

- Management can export current Branch donors and category settings.
- Imports use documented, stable headings and validate rows before applying changes.
- Imports are Branch-scoped and audited.
- Broader Kingston migration formats are designed after the Airtable discovery exercise.

## PF-001E: Live collaboration

People working on the same delivery must see saved work without leaving and reopening it.

Acceptance criteria:

- Open delivery lists and active delivery details refresh within five seconds.
- Admin overview and audit history refresh within ten seconds.
- Background refresh does not discard an in-progress local form.

## Remaining work

- Deeper cross-Branch reporting and Organisation dashboards beyond current stock totals.
- Stronger database-level isolation policies in addition to server-side scoping.
- Push-based events if polling no longer meets operational scale.
