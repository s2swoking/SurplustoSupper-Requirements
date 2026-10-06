# Requirements Guide

These Wiki pages are the collaborative product requirements for Surplus to Supper. They are written for public review without access to another documentation service.

## Status model

Requirement maturity:

- `Draft`: under discussion and open to change.
- `Approved`: agreed by the product owner and operational stakeholders.
- `Superseded`: retained for history but replaced by a linked requirement.

Implementation status:

- `Built`: implemented and verified against the documented requirement.
- `Partial`: some acceptance criteria are implemented.
- `Planned`: not implemented.
- `Architecture gap`: the current technical model does not yet support the requirement.

## Review workflow

1. Create a branch for the documentation change.
2. Edit the relevant requirement and update its last-reviewed date.
3. Open a pull request describing the business decision, not only the wording change.
4. Ask the product owner and an operational representative to review it.
5. Change a requirement from `Draft` to `Approved` only after that agreement is recorded in the pull request.

## Writing rules

- Give each requirement a stable ID and do not reuse retired IDs.
- Separate business rules from current implementation details.
- Keep unresolved points under `Open questions`.
- Use testable acceptance criteria where possible.
- Update the catalogue in [Business Requirements](Business-Requirements) when adding or superseding a requirement.

## Structure

- [Background](Background)
- [Scope](Scope)
- [Platform foundations](Platform-Foundations)
- [Feature pages](Home#operational-features)
- [Reference data](Home#reference-data)
