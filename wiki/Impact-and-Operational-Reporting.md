# F-006: Impact and Operational Reporting

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Planned |
| Actor | Organisation Admin, Branch Manager, Volunteer |
| Last reviewed | 19 October 2026 |

## Purpose

Provide trustworthy reports on food received, stock movement, redistribution, waste, donor participation, recipient reach, meal equivalents, and CO2e impact using data captured by Surplus to Supper (S2S).

Foodiverse impact, food-category, donor-KPI, daily-compliance, and product-log reports informed this requirement, but their donor-network roles, labels, leaderboards, price reporting, and self-service donor/charity views are not copied because they do not match the current S2S inputs, operating model, or identity model.

## Reporting principles

- Reports use completed, immutable operational movements rather than draft records or manually entered headline totals.
- Food received, food redistributed, returns, waste, and stock on hand are separate measures and must not be presented as interchangeable.
- Every weight is reported in normalized kilograms while retaining the quantity and unit originally entered.
- Reports distinguish weight calculated from a category average from directly entered or measured weight.
- Meal-equivalent and CO2e figures are estimates, not actual meals served or directly measured emissions.
- Impact is recognized at one configured event only: completed Intake or completed Redistribution. The same food must not be counted at both events.
- Factor values and methodology versions are snapshotted or referenced on the qualifying movement so historical reports are reproducible.
- Branch access is enforced automatically from the signed-in user's S2S membership; users do not choose a Foodiverse-style `User View` to expand their access.
- Report totals must reconcile to the underlying detail rows and inventory ledger.
- Empty, excluded, or not-applicable values are shown explicitly rather than silently treated as zero.

## Standard metric definitions

| Metric | Definition | Primary source |
| --- | --- | --- |
| Food received (kg) | Sum of normalized kg on submitted Intake lines for food categories | Delivery, Intake line, Category |
| Non-food received (kg) | Sum of normalized kg on submitted Intake lines whose category is excluded from food impact reporting | Delivery, Intake line, Category |
| Gross dispatched (kg) | Sum of normalized kg on dispatched outbound movements | Allocation and outbound movement |
| Returned (kg) | Sum of normalized kg returned against dispatched lines | Return movement |
| Net redistributed (kg) | Gross dispatched kg minus returned kg | Outbound and return movements |
| Expired/wasted (kg) | Sum of normalized kg on negative movements with an approved waste reason, initially `Expired` | Inventory adjustment |
| Current stock (kg) | Opening stock plus inbound and returns, less dispatch and negative adjustments | Inventory ledger |
| Meal equivalents | Impact-eligible kg multiplied by the effective meals-per-kg factor | Qualifying movement and factor snapshot |
| CO2e impact (kg) | Impact-eligible kg multiplied by the effective kg-CO2e-per-kg factor | Qualifying movement and factor snapshot |
| Participating donors | Distinct donors with at least one submitted delivery containing positive food kg in the period | Donor and Delivery |
| Recipients served | Distinct recipients with at least one completed fulfilment containing positive net redistributed kg in the period | Recipient and Fulfilment |
| Factor coverage | Percentage of otherwise eligible kg with valid meal and CO2e factors | Category and factor snapshot |

`Impact-eligible kg` is the normalized food weight at the Organisation's configured recognition event. The recommended S2S default is completed Redistribution because it excludes food that was received but subsequently expired or was otherwise lost. The final recognition event remains a product-owner decision.

## Report suite

### 1. Impact overview

Purpose: give Management a concise view of S2S activity and estimated impact.

The report displays:

- Food received, net redistributed, returned, and expired/wasted kg.
- Meal equivalents and CO2e impact for the configured recognition basis.
- Participating donors and recipients served.
- Factor coverage and a visible methodology/version label.
- A time-series chart using daily, monthly, or yearly buckets.
- Category and Branch breakdowns.

The report must label its recognition basis, for example `Impact recognized on completed redistribution`, beside the totals.

### 2. Food-flow and inventory reconciliation

Purpose: prove how opening stock became closing stock and expose missing or duplicated movements.

For each Branch and period:

`Opening stock + received + returns + positive adjustments - dispatched - negative adjustments = closing stock`

The report displays each component in kg and, where meaningful, crates or units. A reconciliation difference must be zero; any non-zero result is a data-quality exception with drill-through to contributing movements.

Period inflow/outflow totals must not be described as a redistribution rate without accounting for opening and closing stock.

### 3. Donor contribution report

Purpose: show what each donor supplied to S2S without creating a competitive leaderboard.

The report displays:

- Donor and donor type.
- Number of submitted deliveries.
- Food and non-food kg received.
- Category breakdown.
- First and most recent completed delivery in the period.
- Meal/CO2e estimates only when Intake is the configured impact-recognition event; otherwise these remain Organisation-level redistribution measures and are not attributed to a donor without auditable stock-lot lineage.

### 4. Recipient distribution report

Purpose: show which charities and projects received stock from S2S.

The report displays:

- Recipient, project type, and fulfilment method.
- Number of completed fulfilments.
- Gross dispatched, returned, and net redistributed kg.
- Category breakdown.
- Meal equivalents and CO2e impact when Redistribution is the configured recognition event.

`Recipients served` counts distinct recipient records, not individual beneficiaries. S2S must not infer people reached from crate count, kilograms, or recipient capacity.

### 5. Waste and adjustment report

Purpose: identify avoidable loss and explain manual stock changes.

The report displays:

- Expired/wasted kg by date, Branch, category, use-by date, and reason.
- Miscount increases and decreases separately from waste.
- Returns from charities separately from new donations.
- Adjustment quantity, entered unit, normalized kg, reason, actor, and timestamp.
- Links to the original stock lot and any compensating reversal.

Only adjustments with an approved waste reason contribute to the waste metric. Miscounts and corrections do not.

### 6. Impact and movement detail

Purpose: provide the auditable rows behind every summary total and CSV export.

Detail columns include:

| Field | Notes |
| --- | --- |
| Recognition/Movement Date | Date used for the selected report metric |
| Branch | Enforced by access scope |
| Movement ID and Type | Intake, dispatch, return, or adjustment |
| Donor and Delivery ID | Populated for inbound lineage |
| Recipient and Fulfilment ID | Populated for outbound lineage |
| Category and Use-by Date | Stock classification |
| Entered Quantity and Unit | Original crates, kg, or units |
| Normalized Weight (kg) | Weight included in calculations |
| Weight Basis | Category-average calculation, direct kg, or measured weight |
| Impact Eligibility | Included, excluded, or factor missing, with reason |
| Meals Factor and Equivalent Meals | Factor snapshot/version and calculated value |
| CO2e Factor and Impact | Factor snapshot/version and calculated value |
| Adjustment Reason | Populated for adjustment rows |
| Recorded By and At | Management-only detail where required by access policy |

### 7. Category and storage-flow report

Purpose: show the composition and movement of food using the S2S category catalogue.

The report displays:

- Completed received, net redistributed, expired/wasted, and current-stock kg by category.
- Percentage of the selected total represented by each category.
- Storage-family grouping such as Ambient, Chilled, Frozen, or Hot only where that classification is explicitly configured.
- Trends by day, month, or year.
- A totals table behind any chart so small categories and exact values remain accessible.
- CSV export of category and storage-family totals and detail.

The user selects one movement basis at a time, such as `Food received` or `Net redistributed`; the report must not combine stages into a single ambiguous total. S2S reports completed operational quantities and does not show Foodiverse-style `Estimate versus Actual` columns because no pre-donation estimate is captured. It also excludes price because S2S does not record product value.

### 8. Donor cadence and delivery reliability report

Purpose: help Branch Management identify expected deliveries, missed collections, and incomplete Intake without treating donors as competitors.

When structured donor cadence and expected-delivery occurrences are available, the report displays:

- Expected delivery occurrences in the selected period.
- Delivery IDs created for expected occurrences.
- Submitted deliveries completed.
- Open deliveries still awaiting completion.
- Cancelled or `No delivery` outcomes, with reason where captured.
- Delivery completion rate: submitted expected deliveries divided by expected occurrences whose response window has closed.
- Received kg and average received kg per completed delivery.
- Daily exceptions requiring follow-up, followed by monthly/yearly trends.

An expected occurrence is not marked missed while its delivery window remains open. Unscheduled ad-hoc deliveries contribute to donor volume but do not increase the scheduled-delivery completion numerator or denominator.

Until structured cadence, generated expected occurrences, delivery windows, and no-delivery outcomes are implemented, S2S can report completed delivery counts and kilograms by donor but must not publish a `compliance` or `reliability` percentage.

## Filters and time periods

All reports support only dimensions captured by S2S:

- Date range, plus presets for current month, current year, and all time.
- Daily, monthly, or yearly grouping appropriate to the selected range.
- One, multiple, or all authorised Branches.
- Donor and donor type.
- Recipient and project type.
- Category and storage family where represented by category configuration.
- Fulfilment method.
- Movement or adjustment reason.
- Expected-delivery outcome when donor cadence reporting is available.

Filters for Foodiverse labels, donor-network memberships, or arbitrary user-view modes are not included because S2S does not currently capture those concepts.

## Permissions

- Organisation Admins can report across authorised S2S Branches and compare Branch totals.
- Branch Managers can view detail and export data for their Branch.
- Volunteers can view the operational and impact summary for their Branch but cannot view actor-level audit detail or export it unless explicitly granted.
- Donor and recipient self-service reports are not included until S2S defines external identities, permissions, and privacy rules.

## CSV export

- Every report can export the currently filtered detail rows and a metadata section containing generation time, Branch scope, filters, recognition basis, factor methodology/version, and units.
- Exported totals must equal the on-screen totals for the same filters.
- A direct CSV download is sufficient initially. Queued generation and email notification are introduced only if measured export size or duration requires them.
- CSV exports are audited with requesting user, Branch scope, filters, row count, and timestamp.

## Data availability and gaps

| Reporting need | S2S input position | Requirement consequence |
| --- | --- | --- |
| Received kg by donor/category/use-by | Captured on submitted Delivery and Intake lines | Can be reported from current required inputs |
| Participating donors | Donor and submitted Delivery references exist | Count distinct donor IDs, not names |
| Redistributed kg and recipients served | Completed fulfilments, outbound movements, and returns exist in the requirements | Report net completed movement only |
| Expired kg | `Expired` adjustment type exists | Broader waste reporting needs an approved waste-reason taxonomy |
| Meal and CO2e factors | Defined as category configuration but not implemented | Do not publish impact estimates until factor coverage and versioning are operational |
| Surplus versus purchased/appeal stock | Not currently captured | S2S must confirm that all food intake is surplus or add an impact-eligibility/source field before relying on this distinction |
| Actual meals served | Not captured | Report only `meal equivalents` |
| Individual beneficiaries or people reached | Not captured | Do not estimate or report this measure |
| Monetary value of food | Not captured | Excluded from reporting |
| Donor/recipient labels and leaderboards | Not captured and not an S2S priority | Excluded unless a later S2S use case is approved |
| Category mix by operational stage | Category and normalized kg exist on stock movements | Report completed received, redistributed, waste, or stock stages separately |
| Estimate versus actual | No pre-donation estimate is captured | Excluded; completed S2S movement values are the operational record |
| Food price/value | No price or valuation input is captured | Excluded from reports and exports |
| Donor cadence/reliability | Delivery cadence is required but structured occurrence generation and outcomes are not implemented | Show donor contribution now; publish rates only after the denominator and outcome rules exist |
| Donor offers/posts | S2S starts from a Management-created Delivery ID rather than a donor offer | Foodiverse offered/posted/process-success metrics do not apply |
| Product-level logs | S2S captures category stock lots, not SKU/product lines | Use movement detail; add product reporting only after a separate approved product-capture requirement |

## Data quality rules

- Draft deliveries and draft/ready allocations are excluded from completed-flow totals.
- A completed movement is counted once using its immutable ID.
- Returns reduce net redistribution and never create a new donor contribution.
- Reversal movements offset the original in the applicable reporting period without deleting history.
- Missing or Not Applicable factors do not default to 2.2 or 3.2 silently; affected kg is disclosed through factor coverage.
- Calculated weight is labelled as calculated in detail and export data; it must not be described as scale-measured weight.
- Report calculations use unrounded stored values and round only for display/export according to the approved precision.
- Branch-local dates use the Branch timezone; cross-Branch reports state the timezone used for grouping.
- Category, donor, and recipient renames do not alter historical identity because reports group by stable IDs.
- Donor reliability rates use generated expected-occurrence IDs, not an inferred count of weekdays or a count of manually created Delivery IDs.
- Ad-hoc deliveries are identified separately from scheduled deliveries so they do not distort cadence rates.

## Acceptance criteria

- `F-006-AC1`: Every summary total can be reproduced from its filtered detail rows.
- `F-006-AC2`: Submitted Intake contributes to received kg once; open Intake contributes nothing.
- `F-006-AC3`: Net redistributed kg equals completed outbound kg less linked returns.
- `F-006-AC4`: Meal and CO2e totals use the effective factor snapshot and configured recognition event without double-counting.
- `F-006-AC5`: Missing factors reduce factor coverage and are visibly excluded rather than silently defaulted.
- `F-006-AC6`: Participating donors and recipients served count distinct stable records with qualifying activity.
- `F-006-AC7`: Inventory reconciliation equals zero or exposes a drill-through data-quality exception.
- `F-006-AC8`: Miscount corrections do not increase the waste metric, while approved waste reasons do.
- `F-006-AC9`: Branch permissions constrain on-screen results and CSV exports identically.
- `F-006-AC10`: A CSV export matches the selected filters, totals, units, and methodology shown on screen.
- `F-006-AC11`: The interface and export label estimates as `meal equivalents` and `CO2e impact`, not actual meals served or measured emissions saved.
- `F-006-AC12`: Reports do not expose donor, recipient, or actor data outside the user's authorised Branch scope.
- `F-006-AC13`: Detail rows and CSV exports identify whether normalized kg was calculated, entered directly, or measured.
- `F-006-AC14`: Category totals for a selected movement basis reconcile to the corresponding report and ledger detail.
- `F-006-AC15`: The category report does not present completed S2S quantities as donor estimates or monetary value.
- `F-006-AC16`: A donor delivery-completion rate is shown only when expected occurrences and closed response windows provide a valid denominator.
- `F-006-AC17`: Open, cancelled/no-delivery, missed, completed, and unscheduled deliveries remain distinct in donor cadence detail.
- `F-006-AC18`: Product-level and price reports remain unavailable unless their source inputs are introduced through approved requirements.

## Open decisions

1. Approve completed Intake or completed Redistribution as the single impact-recognition event; completed Redistribution is recommended for conservative reporting.
2. Confirm whether S2S ever receives purchased, appeal, or other non-surplus food and therefore needs a stock-source/impact-eligibility field.
3. Approve the category factor source, effective date, precision, review owner, and change process.
4. Approve the waste-reason taxonomy beyond `Expired`.
5. Confirm whether Volunteers may export Branch summary/detail data.
6. Confirm the timezone and period-locking policy for cross-Branch reports.
7. Decide how structured donor cadence creates expected occurrences or Delivery IDs and approve the cancellation/no-delivery reason taxonomy.
8. Confirm whether storage family is an explicit category attribute or a stock-line attribute before grouping category reports by Ambient, Chilled, Frozen, or Hot.

## Current implementation

Operational dashboards and stock totals exist, but the complete report suite, factor versioning, factor coverage, multi-Branch comparison, report-detail drill-through, and audited filtered CSV exports remain to be built.
