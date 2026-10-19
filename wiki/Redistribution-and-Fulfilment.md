# F-005: Redistribution

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Core flow built; future capabilities specified |
| Actor | Management, Volunteer |
| Last reviewed | 19 October 2026 |

## Terminology boundary

`Redistribution` is the S2S user-facing term for moving available stock from an S2S Branch to a recipient charity or project. The following language applies consistently in screens, notifications, exports, reports, audit events, and support material:

- Food arriving from a donor is a `Donor Delivery` and is processed through `Intake`.
- Food leaving S2S for a recipient is a `Redistribution`.
- The outbound identifier is a `Redistribution ID`.
- The final evidence is a `Redistribution Record`.
- The two methods are `S2S Delivers` and `Recipient Collects`.
- A redistribution is `Planned` when linked to an expected recipient occurrence and `Ad hoc` otherwise.

The external reference material uses a different term for its donor-to-charity marketplace workflow. That term must not become an S2S entity or user-interface label. Any future integration translates external terminology at the boundary while preserving the external source value only as technical provenance.

`Fulfilment` may remain an internal legacy implementation name during migration, but new user-facing work and API documentation use `Redistribution`.

## S2S capability decisions

| External pattern considered | S2S decision | Reason |
| --- | --- | --- |
| Donor self-posting and recipient offer acceptance | Exclude | S2S receives stock through a Management-created Delivery ID and Intake; it does not operate an offer marketplace |
| Category quantities | Adapt | Allocate existing S2S stock lots by category in crates, kilograms, or units |
| Scheduled and unplanned transfers | Adapt | Support Planned and Ad hoc Redistribution records without requiring donor posting windows |
| Several collectors for one offer | Adapt | A redistribution plan may be split across recipients, but each recipient has its own Redistribution ID and stock movements |
| Transfer confirmation | Include | S2S needs explicit dispatch, completion, and unsuccessful-outcome controls |
| Transfer record and signature | Adapt | Provide an auditable Redistribution Record confirmed by the S2S operator; recipient signature is not required |
| Email receipts | Exclude | S2S does not automatically message the recipient; authorised users can view and print the internal Redistribution Record |
| Product-level detail and price | Exclude | S2S currently controls category/use-by stock lots and does not capture product catalogue or valuation data |
| Historical bulk logger | Exclude here | S2S will not import old Redistribution history; spreadsheet creation of new draft Intake and Redistribution records is a separate platform capability |

## Preconditions

- A recipient charity or project is active for the Branch.
- The recipient has an approved category profile, capacity, redistribution method, and service window.
- Eligible, unexpired stock is available in inventory.
- The user's active membership grants access to the Branch.

## Basic flow

1. The user starts from an expected recipient occurrence or creates an Ad hoc Redistribution.
2. The user chooses one active recipient, service date, method, and service window.
3. The user adds one or more eligible stock-lot lines and chooses Crate, Kilogram, or Unit from the units supported by that category and stock lot. The system recommends first-expiring stock first.
4. The system previews the entered quantity, canonical physical quantity where available, normalized kg, and conversion snapshots, then validates accepted categories, storage capability, capacity, use-by, and available unreserved stock.
5. Saving as `Draft` reserves stock without reducing stock on hand.
6. The user marks the Redistribution `Ready` after its lines have been checked and prepared.
7. Dispatch posts immutable outbound inventory movements and records who released the stock and when.
8. The S2S user confirms the handover as `Completed`.
9. If the intended handover did not happen, Management marks it `Not completed`; the system posts linked reversal movements that restore the entire dispatched quantity to inventory.
10. A terminal outcome generates an immutable Redistribution Record.

## Planning and occurrence rules

- An expected recipient occurrence is generated only from an active, structured recipient cadence.
- The expected occurrence identifies Branch, recipient, intended date/window, and method but does not reserve stock.
- Creating a Redistribution from an expected occurrence links the two records; it does not silently mark the occurrence completed.
- One expected occurrence can have at most one active Redistribution unless an authorised user records and explains a replacement.
- An Ad hoc Redistribution is permitted and clearly tagged so cadence reporting remains valid.
- A missed occurrence is recorded only after its service window closes without a Completed Redistribution or approved cancellation outcome.
- S2S does not use offer-posting, accept-by, primary-recipient, wait-list, or food-board workflows.

## Allocation and quantity rules

- One Redistribution belongs to one Branch and one recipient.
- A Redistribution can contain multiple stock-lot lines and categories.
- Each line records the entered quantity and unit (`crate`, `kg`, or `unit`) plus normalized kilograms where conversion is available.
- Crate and Unit are available together only for a unit-enabled category with effective Units per Crate and Average kg per Crate values.
- For a unit-enabled stock lot, reservations and dispatch use the canonical individual-unit balance. A crate entry reserves `crate quantity × units per crate`; a unit entry reserves the entered unit count.
- A crate-origin stock lot can therefore be redistributed as whole crates, individual units, or a combination without double-reserving the same stock.
- A kilogram-only stock lot can be redistributed only in kilograms unless an authorised recount establishes a physical crate/unit count. S2S does not derive an exact count from average weight alone.
- A crate-only stock lot can be redistributed in crates or measured kilograms according to the approved split/recount rule, but not in individual units unless Unit is enabled.
- The confirmation preview shows entered quantity/unit, units-per-crate snapshot where applicable, normalized kg, and whether weight is calculated, directly entered, or measured.
- Category, use-by date, storage state, and inbound lineage are inherited from the selected stock lot rather than retyped as unverified product data.
- Only categories accepted by the recipient can be allocated.
- Chilled, frozen, or hot stock can be allocated only where the recipient's approved storage capability permits it.
- Allocation cannot exceed recipient capacity, unreserved stock, or stock on hand.
- Draft and Ready Redistributions reduce available stock but do not reduce stock on hand.
- Dispatch is atomic and cannot make any stock lot negative.
- A planner may prepare a multi-recipient batch, but the system creates a separate Redistribution ID, lifecycle, record, and set of movements for every recipient.
- Splitting a line across recipients cannot reserve or dispatch more than the source quantity.

## Lifecycle and outcomes

| Status | Meaning | Stock effect |
| --- | --- | --- |
| Draft | Lines can be edited; quantities are reserved | Available stock reduced; stock on hand unchanged |
| Ready | Checked and prepared for release | Reservation retained |
| Dispatched | Stock has left S2S control | Immutable outbound movement reduces stock on hand |
| Completed | Recipient handover confirmed | No additional stock movement unless a linked return is recorded |
| Cancelled | Work stopped before dispatch, with reason | Reservation released |
| Not completed | Management confirms that the intended handover did not happen | Linked reversal movements restore every dispatched line to inventory |

- Only Draft or Ready records can be cancelled.
- A terminal record cannot be edited silently. Corrections use linked inventory movements and an auditable addendum.
- Only Management can mark a Dispatched Redistribution `Not completed`.
- `Not completed` requires a controlled reason such as recipient unavailable, recipient rejected, vehicle issue, or other with explanation.
- Marking a Redistribution `Not completed` restores the full dispatched quantity to its source stock lots through immutable linked reversal movements. Any subsequent expiry, damage, or other loss is recorded separately through Inventory Adjustment.
- Partial acceptance completes the accepted quantity and records the remainder as linked return movements; it is not represented by overwriting dispatched quantities.
- Repeated completion requests are idempotent and cannot post inventory twice.

## Redistribution Record

The Redistribution Record is available after `Completed` or `Not completed` and contains:

| Field | Notes |
| --- | --- |
| Redistribution ID | System-generated stable reference |
| Branch and recipient | Names plus stable identifiers |
| Planned/Ad hoc | Includes expected-occurrence reference where applicable |
| Method and service window | `S2S Delivers` or `Recipient Collects` |
| Status history | Created, Ready, Dispatched, terminal outcome, actors, and timestamps |
| Category and stock-lot lines | Entered quantity/unit, normalized kg, use-by date, and storage state |
| Inbound lineage | Source Delivery ID and stock-lot reference where available |
| Returns or other resolutions | Linked movement, quantity, reason, actor, and time |
| Operational confirmation | S2S actor and timestamp that confirmed completion, or Management actor and reason for Not completed |
| Food-safety information | Included only from approved captured source data; never inferred |
| Audit addenda | Corrections or explanatory notes without changing the original event |

- Management and authorised Branch users can view and print the record.
- S2S does not automatically send the Redistribution Record or another message to the recipient.
- A regenerated record reproduces the values and factor/conversion references effective at the recorded events.

## History and search

Authorised users can search Branch Redistribution history by:

- Date range and service window.
- Redistribution ID.
- Recipient and project type.
- Status and unsuccessful-outcome reason.
- Planned or Ad hoc source.
- Method.
- Category, storage state, and use-by date.

Summary and exported totals reconcile to the immutable movement detail. History does not expose another Branch's records.

## Audit and permissions

- Every creation, line change, reservation, status transition, return, operational confirmation, export, and spreadsheet-created draft is auditable.
- Volunteers can perform operational steps for their active Branch but cannot change recipient policy, cadence, category eligibility, or spreadsheet-import rules.
- Management controls cancellations after Ready and Not completed outcomes.

## Acceptance criteria

- `F-005-AC1`: Concurrent Redistributions cannot reserve more quantity than is available in any stock lot.
- `F-005-AC2`: Draft, Ready, Dispatched, Completed, Cancelled, and Not completed follow only valid transitions.
- `F-005-AC3`: Dispatch reduces inventory exactly once, including when a completion request is retried.
- `F-005-AC4`: A linked return restores the correct stock lot and cannot exceed the dispatched quantity.
- `F-005-AC5`: Ineligible category, storage incompatibility, inactive recipient, wrong method, expired stock, and capacity violations are rejected.
- `F-005-AC6`: A Redistribution can contain several lines and preserves each line's entered unit, normalized kg, use-by date, and stock lineage.
- `F-005-AC7`: Planned and Ad hoc Redistributions remain distinguishable in history and reports.
- `F-005-AC8`: Splitting a plan across recipients creates separate Redistribution IDs without duplicating stock.
- `F-005-AC9`: Only Management can mark a Dispatched Redistribution Not completed, and doing so restores the entire dispatched quantity through linked reversal movements.
- `F-005-AC10`: Partial acceptance retains the dispatched event and represents the unaccepted quantity with linked returns.
- `F-005-AC11`: A terminal outcome produces a reproducible Redistribution Record with status history and movement detail.
- `F-005-AC12`: Food-safety information is displayed only when captured from an approved source, and operational confirmation records the responsible S2S actor and timestamp.
- `F-005-AC13`: Completion does not depend on a recipient signature, recipient account, or outbound recipient message.
- `F-005-AC14`: Branch permissions constrain history, records, operational confirmations, and exports consistently.
- `F-005-AC15`: No S2S redistribution screen, notification, export heading, or record uses the external platform's entity terminology.
- `F-005-AC16`: S2S does not import old Redistribution history through this workflow; spreadsheet-created new records enter as Draft and follow the normal lifecycle.
- `F-005-AC17`: A user can redistribute an eligible stock lot in Crates, Kilograms, or Units only when the category and stock-lot tracking basis support the selected unit.
- `F-005-AC18`: Crate and Unit lines against the same unit-enabled stock lot reserve and dispatch from one canonical unit balance without oversubscription.
- `F-005-AC19`: A kilogram-only stock lot cannot be redistributed as crates or units unless an authorised recount establishes the physical count.
- `F-005-AC20`: The line preview and final Redistribution Record preserve the entered unit, conversion snapshots, normalized kg, and weight basis.

## Open decisions

1. Confirm the recipient cadence model and how expected occurrences are generated, cancelled, or rescheduled.
2. Approve the unsuccessful-outcome and stock-resolution reason taxonomies.
3. Define the approved source and mandatory fields for allergen and food-safety information; S2S must not infer these from category.
4. Decide whether multi-recipient planning needs a visible parent batch or only linked Redistribution records.

## Current implementation notes

- Branch recipients, accepted categories, capacity, method, service window, Draft reservation, Ready, Dispatch, Completed, Cancelled, returns, and immutable outbound movements exist.
- The current UI creates one stock-lot line at a time; the API and data model support multiple lines.
- Planned occurrences, recipient cadence, multi-recipient batches, Management-controlled Not completed outcomes with full reversal, generated Redistribution Records, historical search enhancements, spreadsheet-created Draft records, kilogram/unit entry, and canonical unit reservations remain to be built.
- Partial acceptance is currently represented by recording returned crates after dispatch.
