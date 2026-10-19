# SC-001: Product Scope and Capability Map

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Core operations built |
| Last reviewed | 19 October 2026 |

## Donors

Supermarkets, retailers, restaurants, and other donors provide surplus on a recurring cadence. Arrival time may be confirmed by telephone a day ahead, but crate count and contents remain unknown until unloading. This is an operating constraint, not missing upstream data.

Required capability:

- Onboard and maintain donors.
- Record expected delivery cadence.
- Create an operational Delivery ID when a delivery is expected or arrives.

## Intake

Crates are unloaded and staged by category. Mixed crates are manually re-sorted so each staged crate represents one category before stock moves onward.

Required capability:

- Start receiving against an existing Delivery ID.
- Record crates systematically by category, weight, and use-by date.
- Review and complete the receiving process.

## Inventory

Received stock may move directly to an outbound van or into storage. The largest current control gap is the lack of systematic use-by tracking once stock is stored.

Required capability:

- View current warehouse stock.
- Break stock down by category, weight, and use-by urgency.
- Record expiry, miscount, and charity-return adjustments in crates, kilograms, or units with an audit trail.

## Redistribution

Standing projects include Foodbank, FoodHub, Cooks, Breakfast Club, Wraparound Club, Coffee Mornings, and Social Supermarket. Each has an accepted-category profile, capacity, delivery window, and cadence. Allocation is currently manual and repeated as stock arrives.

Required capability:

- Onboard recipient charities and projects.
- Allocate eligible stock.
- Assign and load crates to a van or collection.
- Close the outbound movement and reduce inventory.
- Record partial acceptance and returned stock.

For MVP planning, Kitchen and Markets may be represented as recipient projects. Their specialised workflows remain later work.

## Supporting capabilities

- Authentication and role-based access for Management and Volunteers.
- Operational dashboards.
- Auditable impact, donor, recipient, waste, and inventory-reconciliation reports with filtered CSV export.
- Organisation and Branch administration.

## Delivery boundary

The current application covers branch creation/switching, donor setup, Delivery ID intake, category weights, immutable inventory, controlled adjustments, recipient onboarding, manual allocation, loading, dispatch, completion, returns, use-by alerts, and operational dashboards.

Still outside the current boundary are automated allocation, route/van optimisation, recurring charity cadence, partial acceptance beyond a returned-crate movement, stock transfers, and deeper Organisation reporting. Multi-unit inventory adjustments are now an agreed requirement but are not yet implemented.
