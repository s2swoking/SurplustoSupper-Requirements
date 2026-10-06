# BG-001: Operating Background and Tenancy

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Foundation built |
| Last reviewed | 5 October 2026 |

## Background

Surplus to Supper is a registered charitable incorporated organisation that supports people in need across Surrey and London through surplus food and essential items. It is volunteer-led, with more than 200 volunteers, and currently operates from Sunbury and Kingston. Kingston Voluntary Hub operates Surplus to Supper in Kingston.

S2S has four principal operations:

- Redistribution: collect surplus food from local businesses and deliver it to front-line charities and community groups.
- Kitchen: turn food that might otherwise go to landfill into free meals.
- Market: operate weekend surplus-food markets by donation, with proceeds supporting the van fleet.
- Events: provide corporate and private catering as a funding channel for the wider operation.

Woking is a new hub operated by FoodWise under the Surplus to Supper banner. Existing hubs rely heavily on manual processes; Kingston also uploads information into Airtable for dashboard reporting. Woking needs an operational platform that begins with inbound intake and can grow into inventory and redistribution.

## Architecture requirement

The system must model Woking as a `Branch` belonging to the Surplus to Supper `Organisation`, not as the application's implicit and permanent tenant.

Onboarding another hub must involve creating or associating an Organisation and one or more Branches, without introducing a new tenancy model or migrating branch-owned operational records.

## Acceptance criteria

- `BG-001-AC1`: Every operational record that belongs to a location can be associated with a Branch.
- `BG-001-AC2`: A Branch belongs to an Organisation.
- `BG-001-AC3`: Adding Sunbury or Kingston does not require a schema redesign.
- `BG-001-AC4`: Branch boundaries can later support branch-specific users, donors, deliveries, inventory, and recipients.

## Current implementation

The schema now contains Organisation and Branch entities, seeds Surplus to Supper and Woking, and backfills the original prototype records into Woking. Donors, deliveries, memberships, category settings, API queries, and audit events are branch-scoped. Branch creation, branch switching, cross-branch reporting, and cross-branch stock transfer remain future work.
