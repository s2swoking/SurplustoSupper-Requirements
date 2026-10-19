# F-004: Charity Onboarding

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Core flow built |
| Actor | Management |
| Last reviewed | 6 October 2026 |

Kitchen and Markets may be onboarded as recipient records for MVP redistribution, using distinct project types. Their specialised workflows remain outside this requirement.

## Preconditions

- Categories are configured.
- Project types are configured.
- Any legal, safeguarding, registration, or contractual activity has been completed outside the system.

## Trigger

Management selects `Add charity`.

## Basic flow

1. Management enters the recipient's operational details.
2. The system validates mandatory fields.
3. The system creates the recipient with status `Active`.
4. The recipient becomes available for allocation and outbound loading.

## Business rules

- Only Management can create or maintain recipient records.
- Only active recipients appear in allocation and loading workflows.
- Accepted categories constrain what can be allocated.
- Branch, capacity, delivery window, and Redistribution method constrain Redistribution planning.
- Inactivation does not change historical allocations or deliveries.

## Data specification

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| Charity ID | System-generated | Auto | Primary key |
| Charity Name | Text | Yes |  |
| Project Type | Enum | Yes | Foodbank, FoodHub, Cooks, Breakfast Club, Wraparound Club, Coffee Mornings, Social Supermarket, Kitchen, Market |
| Registration Required | Boolean | Yes | Determined or defaulted by project type |
| Branch | Branch reference | Yes | Woking for the initial rollout |
| Accepted Categories | Category references | Yes | Gates allocation |
| Crate Capacity | Positive integer | Yes | Capacity per Redistribution event; final meaning to confirm |
| Delivery Window | Time range | Yes |  |
| Redistribution Method | Enum | Yes | S2S Delivers or Recipient Collects |
| Contact Name | Text | No |  |
| Contact Phone | Text | Yes |  |
| Status | Active/Inactive | Yes | Defaults to Active |
| Onboarded By | User reference | Auto |  |
| Onboarded Date | Date/time | Auto |  |

## Acceptance criteria

- `F-004-AC1`: Missing mandatory details prevent submission.
- `F-004-AC2`: An active recipient is available to eligible allocation workflows.
- `F-004-AC3`: The system prevents allocation of an unaccepted category.
- `F-004-AC4`: An inactive recipient is hidden from new work but retained in history.

## Open questions

- Is crate capacity a total, a per-category limit, or both?
- Does a recipient operate on a recurring cadence that should be modelled separately?
- What evidence is needed when registration is required?

## Current implementation notes

- Management can create and activate/inactivate branch recipients.
- Accepted categories, per-allocation crate capacity, Redistribution method, delivery window, contact details, and registration-required status are stored.
- Project type remains free text until the final taxonomy is approved.
- Delivery window and contact phone are currently optional pending confirmation of mandatory-field rules.
