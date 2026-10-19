# PF-001: Platform Foundations

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Core foundation built; operational spreadsheet workflows planned |
| Last reviewed | 19 October 2026 |

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

## PF-001D: Spreadsheet and CSV data exchange

Spreadsheet-compatible CSV import and export support master-data migration and current operational collaboration. Importing old Intake or Redistribution history is not included. New operational records created from a spreadsheet must enter the same controlled lifecycle as records created on screen.

### Master-data exchange

- Management can export current Branch donors and category settings.
- Master-data imports use documented, stable headings and validate rows before applying changes.
- Broader Kingston master-data migration formats are designed after the Airtable discovery exercise.

### New operational record import

- Intake and Redistribution use separate, versioned, downloadable templates.
- An Intake import adds draft lines only to a valid open Delivery ID. It does not increase inventory until the delivery is reviewed and submitted through the normal Intake workflow.
- A Redistribution import creates a new Draft Redistribution and its stock-lot lines. It cannot import Dispatched, Completed, Cancelled, or Not completed states.
- A Redistribution import validates recipient status, category eligibility, storage compatibility, capacity, stock lot, use-by date, quantity, unit, and unreserved stock before creating the Draft and reservations.
- Imported Draft records follow the same validation, permissions, status transitions, inventory movements, and audit rules as records created on screen.
- Backdated historical operational records and direct stock movements are rejected.

### Validation and audit

- The system validates the whole file before applying it and displays a preview with record, line, entered-quantity, and normalized-kg totals.
- Validation errors identify the row, field, rejected value, and reason.
- If any row fails, no record or reservation from that file is created.
- Duplicate rows, reused client references, duplicate file content, unsupported headings, invalid dates, invalid units, and inaccessible Branch references are rejected.
- Users provide business references only; system identifiers, lifecycle timestamps, audit actors, and movement IDs are assigned by S2S.
- Every import records Branch, actor, template version, original filename, file hash, preview totals, outcome, created record IDs, and timestamp.
- Import and export are Branch-scoped and audited.

Acceptance criteria:

- `PF-001D-AC1`: Management can export current Branch donors and category settings.
- `PF-001D-AC2`: An invalid file creates no records, reservations, or stock movements and provides row-specific errors.
- `PF-001D-AC3`: Intake rows create draft data against an open Delivery ID and affect inventory only after normal submission.
- `PF-001D-AC4`: Redistribution rows create Draft Redistributions only and reserve stock using the normal concurrency controls.
- `PF-001D-AC5`: Imported Draft records cannot bypass permissions, validation, lifecycle, audit, or idempotency controls.
- `PF-001D-AC6`: Retrying the same confirmed file cannot create duplicate operational records or reservations.
- `PF-001D-AC7`: Old completed Intake or Redistribution history cannot be loaded through the operational templates.
- `PF-001D-AC8`: Branch scope is applied identically to preview, import, error output, and export.

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
