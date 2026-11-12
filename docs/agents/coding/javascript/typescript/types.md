# TypeScript Types

Use TypeScript's type system deliberately as part of the architecture and
developer experience.

## Type Ownership

Keep an operation's context and result types close to their owner. Use a
dedicated type file when its contracts form a coherent model with an explicit
subject; a name such as `Subject.types.ts` makes that ownership visible.
Separate unrelated contracts by responsibility instead of collecting them in a
generic `types.ts`. A role name such as `contracts.ts` should identify a real
boundary rather than merely rename the same collection.

## Source Of Truth

Type information should have an identifiable source of truth and flow downstream
through derivation rather than independent redeclaration.

Before defining a type, ask whether that information already exists upstream and
can be derived instead.

Prefer:

```ts
const RESOLUTIONS = ["300", "600", "1200"] as const;

type RESOLUTIONS = typeof RESOLUTIONS;
type Resolution = RESOLUTIONS[number];
namespace Resolution {
  export type Pick<T extends Resolution> = Extract<Resolution, T>;
}
```

over independently maintaining equivalent information:

```ts
const RESOLUTIONS = ["300", "600", "1200"] as const;

type Resolution = "300" | "600" | "1200";
```

Derive only when the dependency is semantically real. Concepts that merely
happen to have the same shape should remain independent.

## Object Types And Generic Defaults

Choose object types by the values they are meant to accept:

- Use `object` for an object whose properties are unspecified.
- Use `Record<string, never>` for an object with no string-keyed properties.

The `{}` type accepts any non-nullish value, including numbers and strings; it
does not describe an empty object. Empty interfaces have the same issue.

For generic extensions of a base object, use a default compatible with the
base's fields. `object` can serve as an unspecified object extension;
`Record<string, never>` would reject the base's string-keyed properties when
intersected with it.

Omit optional library type arguments when their declared defaults already
express the intended contract. Check the installed declaration before removing
an explicit argument.

## Preserve Information

Do not unnecessarily widen information TypeScript already knows.

When a value must conform to a broader contract while retaining its specific
keys or literals, prefer patterns such as:

```ts
const REGISTRY = {
  // ...
} as const satisfies Registry;
```

over annotations that unnecessarily erase useful inferred information.

Think separately about validation and inference preservation.

## Model Relationships And Invariants

Do not only type individual values correctly. Encode meaningful relationships
between them.

Prefer models that prevent invalid combinations and make illegal domain states
unrepresentable where practical.

Use discriminated unions, mapped types, conditional types, indexed access types,
generics, and other TypeScript capabilities when they accurately express those
relationships.

## Align Runtime And Compile-Time Guarantees

A strong TypeScript codebase keeps runtime behavior and compile-time guarantees
aligned. Do not settle for code that merely passes `tsc`; make `tsc` enforce
the same relationships the runtime code relies on.

When code maps between models, constructs payloads, serializes, deserializes,
normalizes, strips or adds prefixes, or adapts boundary data, preserve enough
type information for TypeScript to verify the meaningful assignments.

If runtime code assumes that a value assigned to `family` is a process family,
the compile-time model should check that same fact. A helper that returns "some
valid stripped string" is weaker than a call site that proves "this destination
field receives its matching domain concept."

Use local `satisfies` checks, destination-typed intermediates, constrained
helpers, indexed access types, mapped types, and discriminated models when they
make those relationships visible to TypeScript.

Runtime validation is for data entering from outside the type graph. It should
complement compile-time checks, not replace them for values authored or
assembled inside the codebase.

## Preserve Relationships Through Abstractions

APIs and utilities should preserve useful caller information whenever practical,
including literal types, keys, tuple structure, discriminants, and input/output
correlations.

Avoid abstractions that unnecessarily turn precise information into broader
types.

## Keep Authored Values In The Type Graph

A strong TypeScript codebase keeps authored values inside the type graph.

When a value is written or assembled in source code, TypeScript should be able
to check it at the point of construction. This includes object literals, string
literals, template literals, records, adapter payloads, persistence documents,
network payloads, and generated-contract inputs.

Runtime validation is for data entering from outside the type graph. It does not
replace static checking for values authored inside the codebase.

Direct literals are encouraged when they remain typed. They are often clearer
than factories, maps, or wrappers:

```ts
const colorMap = "COLOR_MAP_GR" satisfies WorkspaceColorMap;
```

Avoid moving authored values through unchecked APIs before TypeScript sees the
intended type:

```ts
return WorkspaceDocument_Validator.parse({
  colorMap: "COLOR_MAP_GR",
});
```

If construction uses dynamic pieces, keep the final value checked against the
closed destination type:

```ts
const colorMap = `COLOR_MAP_${selectedColorMap}` satisfies WorkspaceColorMap;
```

## Closed Sets

When a domain concept consists of a finite set of runtime values, define the
values once and derive the corresponding types from them.

Prefer feature or domain `specs.ts` files for finite runtime sets, domain
registries, and their derived types.

Keep the runtime values, tuple type, union type, and related namespace helpers
in the same module. Use an uppercase runtime constant, a tuple type with the
same name, and a named union derived through that tuple type:

```ts
export const CHANNEL_TYPES = ["main", "passive", "active", "audio"] as const;

export type CHANNEL_TYPES = typeof CHANNEL_TYPES;
export type ChannelType = CHANNEL_TYPES[number];

export namespace ChannelType {
  export type Pick<T extends ChannelType> = Extract<ChannelType, T>;
}
```

Export `Pick` inside the union's namespace, constrain its parameter to the
union, and use `Extract<Union, Parameter>` as its result. Preserve the intended
public or private visibility of the surrounding declarations. Names of unions
and generic parameters are chosen by their owners.

Use the namespace associated with the domain type for related type-level
utilities and constrained specializations:

```ts
type PassiveChannelType = ChannelType.Pick<"passive">;

type AnalysisChannelType = ChannelType.Pick<"passive" | "active">;
```

Follow this pattern consistently rather than manually reproducing narrower
unions downstream.

When a closed set mirrors an upstream schema or generated protocol, derive it from
that contract or use a compile-time check to keep the selected values constrained
to the upstream vocabulary. A closed set owned locally can use the tuple as its
source of truth.

## Use The Type System For DX

Use `typeof`, `keyof`, indexed access, `Extract`, `Exclude`, mapped types,
conditional types, generics, and other type-system capabilities when they
provide concrete benefits such as:

- eliminating duplicated sources of truth;
- propagating upstream changes automatically;
- improving autocomplete and narrowing;
- enabling exhaustive handling;
- improving refactorability;
- preventing invalid states.

Prefer the simplest solution that preserves the desired guarantees.

## Before Introducing A Type

Ask:

1. Where does this information originate?
2. Does it already exist somewhere upstream?
3. Can it be derived instead of duplicated?
4. Am I unnecessarily widening known information?
5. Are there domain relationships or invariants the type system should encode?
6. Will this abstraction preserve useful inference?
7. If I construct data inside the codebase, does TypeScript check it at the
   construction point before runtime validation or boundary handoff?

Passing `tsc` is the baseline, not the goal. Aim for a coherent type graph with
clear sources of truth and intentional information flow.
