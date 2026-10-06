# EF-002: Maintain Categories

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Partial |
| Actor | Management |
| Last reviewed | 5 October 2026 |

## Trigger

Management adds a category or selects an existing category to edit.

## Basic flow

1. Management enters or edits the category name.
2. Management sets its status.
3. The system validates and saves the category.
4. Active categories become available across Intake, Inventory, and Redistribution.

## Business rules

- Only Management can maintain categories.
- Inactive categories are hidden from new-work selectors.
- Existing records retain references to inactive categories.
- Category names must be unique within the applicable catalogue.

## Data specification

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| Category ID | System-generated | Auto | Primary key |
| Category Name | Text | Yes | Initial values include Fruit, Vegetables, Drinks, Hygiene, Beauty, Cereals |
| Status | Active/Inactive | Yes | Defaults to Active |

## Acceptance criteria

- `EF-002-AC1`: An active category is available in Intake.
- `EF-002-AC2`: An inactive category is hidden from new Intake lines.
- `EF-002-AC3`: Inactivating a category does not damage historical records.
- `EF-002-AC4`: Duplicate category names are rejected.

## Current implementation

The database contains a fixed seeded category list and supports editing average crate weight. Adding, renaming, and inactivating categories is not built. Category labels and icons are also mirrored in application code, which must be removed or redesigned before categories can be fully dynamic.
