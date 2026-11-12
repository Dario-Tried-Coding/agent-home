# Java

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

## Authored Values And Validation

Keep values authored inside the codebase in typed Java models at the point of
construction. Prefer records, DTOs, enums, generated protobuf builders, and
domain value objects over untyped maps, raw `JsonNode` trees, or stringly
payloads that only become checked after a validator sees them.

Runtime validation is for data entering from outside the codebase. It should not
replace compile-time checking for values assembled in Java source.
