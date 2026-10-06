# F-001: Donor Onboarding

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Partial |
| Actor | Management |
| Last reviewed | 5 October 2026 |

## Purpose

Create the system record for a donor after the supply relationship and any required agreements have been established outside the system.

## Preconditions

- The donor has agreed to provide surplus items to S2S.
- The Management user has access to the relevant Branch.

## Trigger

Management selects `Add donor`.

## Basic flow

1. Management enters the donor's details.
2. The system validates mandatory fields.
3. Management records the donor's expected delivery days or cadence.
4. The system creates the donor with status `Active`.
5. The donor becomes available when Management creates a Delivery ID.

## Business rules

- Only Management can create or maintain donors.
- Only active donors can be used for new Delivery IDs.
- Inactivating a donor does not alter historical deliveries.
- A donor belongs to a Branch.

## Data specification

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| Donor ID | System-generated | Auto | Primary key |
| Branch | Branch reference | Yes | Woking for the initial rollout |
| Donor Name | Text | Yes | For example, Costco or KFC |
| Donor Type | Enum | Yes | Supermarket, Retailer, Restaurant, Other |
| Primary Contact Name | Text | No |  |
| Contact Phone | Text | Yes | Used for delivery confirmation |
| Contact Email | Text | No |  |
| Delivery Cadence | Schedule | No | One or more expected delivery days |
| Typical Categories | Category references | No | Planning information only |
| Status | Active/Inactive | Yes | Defaults to Active |
| Onboarded Date | Date/time | Auto |  |
| Notes | Text | No |  |

## Acceptance criteria

- `F-001-AC1`: Missing mandatory fields prevent submission and show a clear validation message.
- `F-001-AC2`: A saved active donor is selectable in Create Delivery ID.
- `F-001-AC3`: An inactive donor is not selectable for a new delivery.
- `F-001-AC4`: Historical deliveries retain the donor name and relationship after inactivation.

## Current implementation

The MVP can add or CSV-import a branch-owned donor with name, contact, delivery day, and notes. Creation/import is audited. Donor type, email, status, and structured cadence are not yet implemented.

## Open questions

- Should a recurring delivery cadence automatically create Delivery IDs, suggest them to Management, or remain informational only?
- Can a donor serve more than one Branch?
