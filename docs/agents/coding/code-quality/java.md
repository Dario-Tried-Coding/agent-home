# Java And Spring

## Boundaries

Keep controllers thin. Put business decisions in services, guards, validators,
runtime stores, or command helpers according to the existing package pattern.

## Spring Components

Prefer constructor injection for new Spring components.

## Type Ownership

Keep context and result models with the capability that owns them. Use a nested
record or class when it is local to one owner; use a named top-level model when
it forms a contract shared by meaningful consumers. Follow existing package and
visibility conventions rather than collecting unrelated models into a generic
types or DTO bucket.

Represent finite domain vocabulary with typed enums or the relevant generated
protobuf types. Preserve upstream protocol ownership instead of maintaining an
independent set of equivalent string constants. Use DTOs and records to make
valid relationships between values explicit; keep authored values typed at
construction as described below.

## REST And Validation

Keep REST DTO validation at the API boundary, and keep compatibility decisions
close to the validator or catalog that owns them.

Keep values authored inside the codebase in typed Java models at the point of
construction. Prefer records, DTOs, enums, generated protobuf builders, and
domain value objects over untyped maps, raw `JsonNode` trees, or stringly
payloads that only become checked after a validator sees them.

Runtime validation is for data entering from outside the codebase. It should not
replace compile-time checking for values assembled in Java source.

## Runtime Profiles

Preserve runtime profile boundaries. A setting belongs in a profile-specific
configuration file only while it diverges by environment or exists for one
profile-specific need; shared conventions belong in `application.yml`.

## Configuration And Test Scenarios

Trace operational settings from their authoritative configuration source through
binding to the observable consumer. Preserve Spring property precedence and
intentional fallbacks; verify explicit overrides reach the behavior they control.
Keep protocol constants and product invariants with their owning capability.

Behavioral tests declare the configuration values their scenarios assume,
independently of deployment files. Dedicated configuration tests use non-default
overrides and independently specified expected outcomes to exercise binding and
behavior together. Cover affected runtime profiles when their composition differs.
