# EF-001: Create Delivery ID

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Core flow built |
| Actor | Management |
| Last reviewed | 5 October 2026 |

## Trigger

Management selects `New delivery`.

## Basic flow

1. Management selects an active donor.
2. The system creates a Delivery record with status `Open`.
3. The system generates a human-readable Delivery ID.
4. The delivery is timestamped and associated with the creating user and Branch.
5. The delivery appears in the Intake list for volunteers.

## Postconditions

An open Delivery ID exists and is ready for crate logging.

## Business rules

- Only Management can create a Delivery ID.
- The donor must be active and belong to the relevant Branch.
- The code must be unique and readable during depot operations.

## Data specification

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| Delivery ID | System-generated | Auto | Primary key |
| Delivery Code | Text | Auto | Current format: `DONOR-MMDD-n` |
| Branch | Branch reference | Yes |  |
| Donor | Donor reference | Yes | Active donor only |
| Status | Open/Submitted | Auto | Defaults to Open |
| Created By | User reference | Auto | Signed-in Management user |
| Created At | Date/time | Auto |  |

## Acceptance criteria

- `EF-001-AC1`: An inactive donor cannot be selected.
- `EF-001-AC2`: A newly created delivery appears in Intake without manual data duplication.
- `EF-001-AC3`: Concurrent creation does not produce duplicate codes.

## Current implementation

Management can create a branch-owned Delivery ID for a donor and volunteers in that Branch can see it under Intake. The creating user is captured and the action is written to audit history.
