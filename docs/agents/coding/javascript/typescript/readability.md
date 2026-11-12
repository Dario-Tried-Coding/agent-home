# TypeScript Readability

## Operations That Extend An API

When a helper exists solely to extend a callable API, expose it through that
API's namespace where the codebase uses that pattern. For an object API, use
methods on the owning object. Give helpers with their own responsibility an
independent owner.

## Conditional Local Values

Use a simple expression, including a straightforward ternary, when it clearly
expresses the choice. When constructing a local value needs several steps or
branches, prefer a `const` initialized by an immediately invoked function
expression (IIFE) over a `let` assigned in each branch, provided a separate
function would not own a meaningful responsibility under the criteria above:

```ts
const label = (() => {
  if (item === undefined) return "Choose an item";
  if (item.archived) return "Archived";

  const name = item.name.trim();

  return name || "Unnamed item";
})();
```

Keep an extracted function when it owns a transformation, invariant, domain
operation, or useful shared behavior. Keep mutable variables when they represent
accumulation, iteration, or later updates, including values assigned by
callbacks. The preference concerns constructing one local result, not all uses
of `let`.

## Guards And Returns

Put a blank line above an early-return guard unless it is the first statement
in the function body. Keep an uninterrupted sequence of early-return guards
together; separate the sequence from preceding work. When a branch only returns
and removing braces preserves the control flow, use `if (...) return ...`:

```ts
function getLabel(item: Item | undefined) {
  if (item === undefined) return "Choose an item";
  if (item.archived) return "Archived";

  const name = item.name.trim();

  if (!name) return "Unnamed item";

  return name;
}
```

A guard that establishes a value's validity and the successful return of that
same value form one operation. Keep them adjacent, including in a required
Context access hook:

```ts
const ctx = useContext(Editor_Context);

if (ctx === undefined) throw new Error("Editor_Provider is required");
return ctx;
```
