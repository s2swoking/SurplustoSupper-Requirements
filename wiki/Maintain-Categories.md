# EF-002: Maintain Categories

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Partial |
| Actor | Management |
| Last reviewed | 19 October 2026 |

## Trigger

Management adds a category or selects an existing category to edit.

## Basic flow

1. Management enters or edits the category name.
2. Management sets its status and permitted inventory units.
3. Management configures the meal-equivalent and CO2e conversion factors per kilogram.
4. The system validates and saves the category settings.
5. Active categories become available across Intake, Inventory, and Redistribution.

## Business rules

- Only Management can maintain categories.
- Inactive categories are hidden from new-work selectors.
- Existing records retain references to inactive categories.
- Category names must be unique within the applicable catalogue.
- Permitted inventory units can include Crate, Kilogram, and Unit.
- A category that permits crate transactions must have an active average crate weight before it can be used at Intake.
- A category that permits unit transactions must have an average unit weight or require a measured total weight when units are recorded.
- Meal-equivalent and CO2e factors are non-negative decimals expressed per kilogram of food; a factor can be marked Not Applicable for a category excluded from that measure.
- The initial factors are 2.2 meal equivalents and 3.2 kg CO2e per kilogram, but Management can amend them when the approved reporting methodology changes.
- Impact calculations retain the factor values or factor-version reference used at the time so historical reports do not change silently.
- The reporting configuration defines one qualifying recognition event, such as completed Intake or completed Redistribution; the same food must not be counted at both events.

## Data specification

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| Category ID | System-generated | Auto | Primary key |
| Category Name | Text | Yes | Selected from the initial catalogue below or added by Management |
| Status | Active/Inactive | Yes | Defaults to Active |
| Permitted Inventory Units | Multi-select enum | Yes | Crate, Kilogram, Unit; at least one required |
| Default Inventory Unit | Enum | Yes | Must be one of the permitted units |
| Average Unit Weight (kg) | Positive decimal | Conditional | Required to calculate kg from unit count unless measured weight is captured with the movement |
| Meals per kg | Non-negative decimal or N/A | Conditional | Initial food-category value 2.2; required when included in meal-equivalent reporting |
| CO2e per kg | Non-negative decimal or N/A | Conditional | Initial food-category value 3.2 kg CO2e; required when included in environmental-impact reporting |
| Factor Effective From | Date/time | Yes | Supports controlled methodology changes |
| Factor Source/Methodology | Text or URL | Yes | Identifies the approved source and version |
| Impact Recognition Event | Enum | Yes | Completed Intake or completed Redistribution; normally set consistently across the Organisation |

## Initial category catalogue

The initial catalogue is based on the supplied Foodiverse reference:

1. Ambient meat
2. Prepared ambient product with meat
3. Ambient fish
4. Prepared ambient product with fish
5. Fresh fruit
6. Prepared ambient fruit
7. Fresh vegetables
8. Prepared ambient vegetables
9. Eggs
10. Bread and bread products
11. Sweet and savoury bakery
12. Whole or milled grain

Management can add, rename, activate, or inactivate categories without a software release.

## Acceptance criteria

- `EF-002-AC1`: An active category is available in Intake.
- `EF-002-AC2`: An inactive category is hidden from new Intake lines.
- `EF-002-AC3`: Inactivating a category does not damage historical records.
- `EF-002-AC4`: Duplicate category names are rejected.
- `EF-002-AC5`: A new installation provides the initial category catalogue listed above.
- `EF-002-AC6`: Management can configure permitted units and meal/CO2e factors for each category.
- `EF-002-AC7`: For 100 kg recorded against factors of 2.2 meals/kg and 3.2 kg CO2e/kg, reporting shows 220 meal equivalents and 320 kg CO2e.
- `EF-002-AC8`: Changing a factor affects transactions from its effective date and does not rewrite historical impact totals.
- `EF-002-AC9`: A stock quantity is included in impact reporting once at the configured recognition event and is not double-counted at Intake and Redistribution.

## Current implementation

The database contains a fixed seeded category list and supports editing average crate weight. Adding, renaming, inactivating, configuring multiple units, and maintaining meal/CO2e factors are not built. Category labels and icons are also mirrored in application code, which must be removed or redesigned before categories can be fully dynamic.
