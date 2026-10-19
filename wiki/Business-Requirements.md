# Surplus to Supper: Business Requirements

Status: Draft

Last reviewed: 19 October 2026

This Wiki is the public source of truth for product requirements. Requirements remain `Draft` until an owner approves them. Implementation status records what the current application does; it does not imply business approval.

## Product context

Surplus to Supper (S2S) redistributes surplus food and essential items from donors to front-line charities and community groups. Woking is a new hub operated by FoodWise under the Surplus to Supper banner. The first product release focuses on recording inbound deliveries reliably before expanding into inventory and redistribution.

The target tenancy model is `Organisation -> Branch`. Woking must be represented as a branch rather than as an implicit single tenant so Sunbury, Kingston, or another hub can be added without redesigning the data model.

## Objectives

1. Record every inbound delivery by category, crate count, calculated weight, and use-by date.
2. Give the depot a shared view of delivery progress when several volunteers are working at once.
3. Maintain a reliable, real-time inventory view and a traceable adjustment history.
4. Support allocation and redistribution to eligible charities and projects in later phases.

## Users

- Management: maintains master data, creates Delivery IDs, reviews operations, and performs controlled adjustments.
- Intake volunteer: logs crates against an existing open Delivery ID.
- Dispatch and loading volunteer: allocates and loads stock for redistribution in a later phase.
- Recipient charity or project: receives, collects, or returns stock in a later phase.

## Requirements catalogue

| ID | Requirement | Maturity | Implementation | Detail |
| --- | --- | --- | --- | --- |
| BG-001 | Operating background and tenancy | Draft | Foundation built | [Background](Background) |
| SC-001 | Product scope and capability map | Draft | Core operations built | [Scope](Scope) |
| PF-001 | Platform foundations | Draft | Core foundation built; operational spreadsheet workflows planned | [Tenancy, access, audit, spreadsheet/CSV, live collaboration](Platform-Foundations) |
| F-001 | Donor onboarding | Draft | Partial | [Donor onboarding](Donor-Onboarding) |
| EF-001 | Create Delivery ID | Draft | Core flow built | [Create Delivery ID](Create-Delivery-ID) |
| EF-002 | Maintain categories | Draft | Partial | [Maintain categories](Maintain-Categories) |
| EF-003 | Maintain crate weights | Draft | Partial | [Maintain crate weights](Maintain-Crate-Weights) |
| F-002 | Intake | Draft | Core flow built | [Intake](Intake) |
| F-003 | Inventory management | Draft | Core flow built | [Inventory management](Inventory-Management) |
| EF-004 | Inventory adjustment | Draft | Partial | [Inventory adjustment](Inventory-Adjustment) |
| F-004 | Charity onboarding | Draft | Core flow built | [Charity onboarding](Charity-Onboarding) |
| F-005 | Redistribution | Draft | Core flow built; future capabilities specified | [Redistribution](Redistribution-and-Fulfilment) |
| F-006 | Impact and operational reporting | Draft | Planned | [Impact and operational reporting](Impact-and-Operational-Reporting) |

## Delivery phases

### MVP: inbound intake

- Google-only sign-in with automatic Woking Volunteer membership and role-controlled screens.
- Organisation and Branch data ownership, initially Surplus to Supper -> Woking.
- Management maintains donors and category weights.
- Management creates a Delivery ID for an active donor.
- Volunteers select an open Delivery ID and save each category entry immediately.
- Management or a volunteer closes the delivery after reviewing its line items.
- Submitted deliveries are read-only.
- Audit history, CSV exchange, and near-real-time shared delivery refresh.

### Phase 2: inventory and redistribution — core flow built

- Inventory by category, weight, and use-by urgency.
- Traceable inventory adjustments.
- Charity and project profiles, including accepted categories and capacity.
- Allocation, van loading, delivery/collection, and outbound logging.
- Use-by alerts and first-expiring-first-out allocation support.
- Donor cadence, expected-delivery reminders, and operational data-quality warnings.
- Versioned spreadsheet templates that create new Draft Intake and Redistribution records without bypassing their normal review and stock-movement controls.

### Later phases

- Partial acceptance beyond recording returned crates.
- Kitchen, market, and event workflows beyond their MVP treatment as recipient projects.
- Cross-branch reporting and stock transfers.
- Offline intake with queued synchronisation.
- Delivery and van-load QR codes.
- Organisation dashboards for rescued weight, reach, waste, expiry, meal equivalents, and CO2e impact using configurable, effective-dated category factors.

## Mandatory platform capabilities

- Google is the only authentication provider.
- A first-time verified Google account receives active Woking Volunteer membership automatically; deactivated accounts remain blocked.
- All active Branch members can access Intake, Inventory, Redistribution, and Dashboard; Organisation Admins and Branch Managers can also access Admin.
- Operational reads and writes must be scoped to the authenticated Branch.
- Material changes must create append-only audit events identifying the actor and Branch.
- CSV import and export are required for migration and operational data exchange.
- Shared delivery work must refresh without requiring users to leave and reopen the screen.

## Cross-cutting business rules

- A crate is the standard Intake unit; controlled inventory adjustments can be entered in crates, kilograms, or individual units according to category configuration.
- Crate weight is category-specific and must not be hardcoded globally.
- Meal-equivalent and CO2e factors are category-specific, expressed per kilogram, effective-dated, and traceable to an approved methodology.
- Donors and categories are managed by Management; volunteers cannot create them during Intake.
- Intake starts from an existing open Delivery ID.
- Each intake line saves immediately so several volunteers can work on one delivery.
- A submitted delivery cannot be edited. Corrections are recorded as inventory adjustments, not silent changes.
- Inactive master records remain available to historical records but do not appear in new-work selectors.
- A chilled category cannot be allocated to a recipient without chilled storage.
- Standard van-ready time is 11:30.
- S2S uses `Donor Delivery` and `Intake` for inbound stock and `Redistribution` for outbound stock; external platform terminology is translated at any integration boundary and is not used as an S2S entity or screen label.

## Open decisions

1. Confirm whether storage state (Ambient, Chilled, Frozen, Hot) belongs to the category or stock line.
2. Confirm how recurring donor delivery days should create or suggest Delivery IDs.
3. Confirm prioritisation rules for allocations across recipient project types.
4. Confirm the relationship with Foodiverse and avoid duplicating donor-side offer management.
5. Confirm whether the S2S rota and volunteer QR check-in tools are part of this product.
6. Approve the reporting methodology, ownership, precision, and review cycle for meal-equivalent and CO2e factors.
7. Confirm whether measured crate/unit weights override configured averages automatically or require approval.

## Out of scope for the current build

- Crate and box return logistics.
- Volunteer rota and QR check-in.
- Donor-side offer posting.
- Automated allocation and route/van optimisation.

## Collaboration

See the [requirements guide](Requirements-Guide) for status definitions and the lightweight GitHub review process.
