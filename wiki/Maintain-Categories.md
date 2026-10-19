# EF-002: Maintain Categories

| Field | Value |
| --- | --- |
| Status | Draft |
| Implementation | Partial |
| Actor | Management |
| Last reviewed | 19 October 2026 |

## Trigger

Management opens the single `Maintain Categories` page to review or edit the category catalogue, operational units, crate/unit conversions, and impact factors.

## Basic flow

1. The page displays all 56 seeded categories in a searchable, filterable table.
2. Management selects the Organisation/Branch context and edits a category row or opens its detail panel.
3. Management sets status, storage family, permitted units, default unit, average kg per crate, and—only where individual-unit handling is appropriate—units per crate.
4. Management optionally configures meal-equivalent and CO2e factors per kilogram with source and effective date; new factors are blank until approved.
5. The page previews derived kg per unit and validation/readiness warnings.
6. The system validates and saves the configuration with an audit event.
7. Active and operationally ready categories become available across Intake, Inventory, and Redistribution.

## Single-page administration

The `Maintain Categories` page is the Management workspace for category configuration; crate weights, unit conversion, and impact factors do not require separate navigation.

- The table shows Category, Active status, Storage family, Permitted units, Default unit, Average kg/crate, Units/crate, Derived kg/unit, Meals/kg status, CO2e/kg status, and readiness.
- Search matches category name and stable key. Filters include Active/Inactive, storage family, unit support, missing conversion, and missing factor.
- Management can edit one row at a time and can bulk-activate/inactivate or bulk-set permitted units only after a confirmation preview.
- Factor values, methodology/source, and effective date are edited together; no value can be activated without its required metadata.
- Validation appears on the affected row and in a page summary. Invalid rows do not block saving an unrelated valid row unless Management is performing a bulk action.
- Readiness is shown separately for operations and impact: a category can be operationally ready while one or both impact factors remain `Unconfigured`.
- Historical factor and conversion versions can be viewed from the category row but cannot be overwritten.

## Business rules

- Only Management can maintain categories.
- Inactive categories are hidden from new-work selectors.
- Existing records retain references to inactive categories.
- Category names must be unique within the applicable catalogue.
- The initial 56 categories have stable keys and are inserted idempotently so rerunning a deployment cannot create duplicates.
- Permitted inventory units can include Crate, Kilogram, and Unit.
- A category that permits crate transactions must have an active average crate weight before it can be used at Intake.
- A category permits individual-unit transactions only when `Unit` is enabled and a positive whole-number `Units per Crate` value is configured.
- For a unit-enabled category, `Derived kg per Unit = Average kg per Crate / Units per Crate`; the derived value is read-only.
- Categories whose packaging does not have a reliable units-per-crate relationship must not enable Unit. A measured-weight-only item may still be handled in Kilograms.
- Crate and unit movements for a unit-enabled stock lot use individual units as the canonical handling count. Crates are converted using the effective units-per-crate snapshot.
- A kilogram-only stock lot does not gain an inferred crate or unit count and can be adjusted or redistributed only in kilograms unless an authorised recount records its physical count.
- Meal-equivalent and CO2e factors are non-negative decimals expressed per kilogram of food; a factor can be marked Not Applicable for a category excluded from that measure.
- Initial meal-equivalent and CO2e factors are blank (`Unconfigured`) for all 56 categories. S2S does not copy example values from an external reference into live calculations.
- An `Unconfigured` factor is different from a zero factor and from `Not Applicable`.
- Impact calculations retain the factor values or factor-version reference used at the time so historical reports do not change silently.
- The reporting configuration defines one qualifying recognition event, such as completed Intake or completed Redistribution; the same food must not be counted at both events.

## Data specification

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| Category ID | System-generated | Auto | Primary key |
| Category Key | Stable text key | Auto | Unique immutable key used by seeds, imports, integrations, and reports |
| Category Name | Text | Yes | Selected from the initial catalogue below or added by Management |
| Status | Active/Inactive | Yes | Defaults to Active |
| Storage Family | Enum | Yes | Ambient, Chilled, Frozen, Hot, Non-food, or Unconfigured pending Management review |
| Permitted Inventory Units | Multi-select enum | Yes | Crate, Kilogram, Unit; at least one required |
| Default Inventory Unit | Enum | Yes | Must be one of the permitted units |
| Average kg per Crate | Positive decimal | Conditional | Branch-effective conversion; required when Crate is permitted |
| Units per Crate | Positive whole number | Conditional | Required when Unit is permitted; omitted for categories not managed as individual units |
| Derived kg per Unit | Calculated decimal | Conditional | Average kg per Crate divided by Units per Crate; read-only |
| Meals Factor Status | Enum | Yes | Unconfigured, Configured, or Not Applicable; defaults to Unconfigured |
| Meals per kg | Non-negative decimal | Conditional | Blank initially; required only when Meals Factor Status is Configured |
| CO2e Factor Status | Enum | Yes | Unconfigured, Configured, or Not Applicable; defaults to Unconfigured |
| CO2e per kg | Non-negative decimal | Conditional | Blank initially; kg CO2e per kg of food; required only when CO2e Factor Status is Configured |
| Factor Effective From | Date/time | Conditional | Required for a Configured factor and supports controlled methodology changes |
| Factor Source/Methodology | Text or URL | Conditional | Required for a Configured factor; identifies approved source and version |
| Impact Recognition Event | Enum | Yes | Completed Intake or completed Redistribution; normally set consistently across the Organisation |

## Initial category catalogue

The initial catalogue is based on the supplied Foodiverse reference but is an S2S-managed catalogue. The 56 labels below are unique and repeated rows across screenshots are listed once. The application seed creates every row exactly once with blank impact factors; Management completes operational unit and factor configuration on the Maintain Categories page.

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
13. Flour and starches
14. Pasta and noodles
15. Breakfast cereals
16. Soy products
17. Ambient
18. Water
19. Ambient juice
20. Soft drinks
21. Tea and coffee
22. Alcoholic beverages
23. Ambient Grocery / Other
24. Fats and oils
25. Confectionary
26. Other grocery
27. Non food
28. Chilled meat
29. Prepared chilled product with meat
30. Chilled fish
31. Prepared chilled product with fish
32. Chilled fruit
33. Prepared chilled fruit
34. Chilled vegetables
35. Prepared chilled vegetables
36. Dairy
37. Chilled products with dairy and eggs
38. Chilled Juice
39. Chilled water based flavoured drinks
40. Chilled Grocery / Other
41. Frozen meat
42. Prepared frozen product with meat
43. Frozen fish
44. Prepared frozen product with fish
45. Prepared frozen fruit
46. Prepared frozen vegetables
47. Frozen products with dairy and eggs
48. Frozen Grocery / Other
49. Ice, sorbet and sherbet
50. Hot meat
51. Prepared hot product with meat
52. Hot fish
53. Prepared hot product with fish
54. Prepared hot fruit
55. Prepared hot vegetables
56. Hot Grocery / Other

Management can add, rename, activate, or inactivate categories without a software release.

## Acceptance criteria

- `EF-002-AC1`: An active category is available in Intake.
- `EF-002-AC2`: An inactive category is hidden from new Intake lines.
- `EF-002-AC3`: Inactivating a category does not damage historical records.
- `EF-002-AC4`: Duplicate category names are rejected.
- `EF-002-AC5`: A new installation provides exactly the 56 unique initial categories listed above, and rerunning the seed creates no duplicates.
- `EF-002-AC6`: On one Maintain Categories page, Management can configure status, storage family, permitted/default units, average kg per crate, units per crate, and meal/CO2e factors.
- `EF-002-AC7`: When Management configures 2 meals/kg and 3 kg CO2e/kg for a category, 100 eligible kg produces 200 meal equivalents and 300 kg CO2e; these values are test inputs, not system defaults.
- `EF-002-AC8`: Changing a factor affects transactions from its effective date and does not rewrite historical impact totals.
- `EF-002-AC9`: A stock quantity is included in impact reporting once at the configured recognition event and is not double-counted at Intake and Redistribution.
- `EF-002-AC10`: All seeded meal and CO2e factors start Unconfigured and do not contribute to impact totals until Management activates approved values.
- `EF-002-AC11`: Enabling Unit requires a positive whole-number Units per Crate and an effective Average kg per Crate.
- `EF-002-AC12`: The displayed kg per Unit equals the effective kg per Crate divided by Units per Crate and cannot be edited directly.
- `EF-002-AC13`: A unit-enabled category snapshots units-per-crate and derived kg-per-unit on each posted movement so later configuration changes do not rewrite history.
- `EF-002-AC14`: A kilogram-only movement does not create an inferred crate or unit count.
- `EF-002-AC15`: Management can distinguish operational-readiness warnings from impact-factor warnings on the category page.
- `EF-002-AC16`: Factor and conversion changes are effective-dated, audited, and preserve earlier versions used by posted movements.

## Current implementation

The database contains a fixed seeded category list and supports editing average crate weight on a separate surface. The 56-item initial catalogue specified above is a requirement and has not been loaded into the current application's database seed, so documenting it does not make the new categories appear in the live system. The single Maintain Categories page, idempotent 56-row seed, stable category keys, storage family, multiple units, units-per-crate conversion, blank factor states, factor versioning, and readiness indicators are not built. Category labels and icons are also mirrored in application code, which must be removed or redesigned before categories can be fully dynamic. Implementing the catalogue therefore requires the application repository, a data migration or seed update, UI/API changes, automated tests, and deployment to the target environment.
