# EF-003: Maintain Crate Weights

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Partial; consolidation planned |
| Actor | Management |
| Last reviewed | 19 October 2026 |

## Preconditions

- The category exists.

## Trigger

Management updates the average crate weight from the single [Maintain Categories](Maintain-Categories) page. A separate crate-weight page is not required in the target experience.

## Basic flow

1. Management selects a category on Maintain Categories.
2. Management enters the average weight in kilograms.
3. Where Unit is enabled, the system also requires Units per Crate and previews the derived kg per Unit.
4. The system validates and saves an effective-dated conversion version.
5. Future Intake, adjustment, inventory, and Redistribution calculations use the updated conversion while posted movements retain their snapshots.

## Business rules

- Only Management can maintain crate weights.
- Each category has one active average weight at a time.
- Weight must be greater than zero.
- Unit-enabled categories also require a positive whole-number Units per Crate value.
- Derived kg per Unit equals Average kg per Crate divided by Units per Crate and is not edited independently.
- Updating an average must not rewrite weight recorded on completed historical movements unless that behaviour is explicitly approved.

## Data specification

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| Category | Category reference | Yes |  |
| Average Weight (kg) | Positive decimal | Yes | Drives intake calculation |
| Units per Crate | Positive whole number | Conditional | Required only when Unit is enabled for the category |
| Derived kg per Unit | Calculated decimal | Conditional | Read-only Average Weight divided by Units per Crate |
| Effective From | Date/time | Yes | Determines which conversion applies to a new movement |
| Updated By | User reference | Auto |  |
| Updated At | Date/time | Auto |  |

## Acceptance criteria

- `EF-003-AC1`: Intake weight equals crate count multiplied by the category's active average.
- `EF-003-AC2`: Zero, negative, and invalid values are rejected.
- `EF-003-AC3`: The change records who made it and when.
- `EF-003-AC4`: When Units per Crate is configured, kg per Unit is derived correctly and displayed before save.
- `EF-003-AC5`: Posted movements retain their conversion snapshots after a later weight or units-per-crate change.
- `EF-003-AC6`: Crate weights and units-per-crate are maintained within Maintain Categories rather than through duplicate configuration pages.

## Current implementation

Management can update or CSV-import a Branch's average crate weight, positive-number validation is enforced, and Intake uses the value. `Updated By`, `Updated At`, and an audit event are captured. This capability is currently separate from category maintenance. Units per Crate, derived kg per Unit, effective-dated conversion history, and the consolidated Maintain Categories experience are not yet implemented.

## Open questions

- When an actual measured crate weight is available, should it override the category average automatically or require Management approval?

Meal-equivalent and CO2e impact factors are maintained per kilogram on the category record; see [Maintain Categories](Maintain-Categories).
