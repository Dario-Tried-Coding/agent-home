# React Rendering Subscriptions

Read this when writing or reviewing React subscriptions, selector hooks,
`useSyncExternalStore`, Zustand stores, custom observable stores, controller or
hub subscriptions, form-store subscriptions, or render-performance-sensitive
state reads.

## Atomic Rendering

The target is maximal render optimization. In React DevTools, a component that
re-renders without any rendered value changing is evidence that the render path
can probably be improved.

Treat every React subscription as a render boundary. Optimizing render behavior
starts at that boundary, not inside the component body after React has already
decided to render.

Prefer making subscriptions more atomic by moving them lower in the tree and
closer to the UI that needs the value. Lower, narrower subscriptions are the
best default shape because unrelated changes have fewer consumers to notify.

When a subscription cannot reasonably move lower, keep it efficient by selecting
the smallest value that consumer needs and comparing that selected value with
the right equality function.

Prefer subscribing to primitives, stable references, ids, flags, counts,
statuses, or other narrow derived values. A broad snapshot/store/context
subscription is acceptable only when the component genuinely renders that broad
shape or the source guarantees stable identity for unchanged selections.

## Context And Core Handles

Use Context to distribute a core handle with a lifetime owned according to
[imperative lifecycles](../../imperative-lifecycles.md). Keep the provider value
referentially stable while its contents are unchanged, including any object that
wraps the core. Publish workflow updates through store subscriptions rather than
replacing the Context value for each transition.

Compiler memoization or explicit memoization can establish stable provider
values; verify the resulting identity and update propagation rather than
requiring a particular hook. An update unrelated to the selected value should
not itself force a render through that subscription;
independent props or Context changes may still legitimately render the consumer.

## Selector APIs

Every reusable reactive source consumed by React should offer a selector-based
subscription API. This applies to Zustand stores, `useSyncExternalStore`
wrappers, custom observable cores, controllers, hubs, runtimes, engines, and
form stores.

The selector must run inside the subscription snapshot function, and the
selected value must be compared there. A hook that subscribes to a broad
snapshot and then applies `useMemo(() => selector(snapshot), [snapshot])`
improves ergonomics but does not prevent renders caused by unrelated snapshot
changes.

If a source does not expose a selector/equality API, prepare that API first,
then migrate consumers to use it. Do not work around a broad subscription with
component-local memoization.

## Optional Sources

Reusable selector hooks should accept `null` and `undefined` when the source's
absence is a normal lifecycle state. Call the hook unconditionally; source
absence belongs inside the subscription adapter. Split components when a mount
boundary is useful for state and effects, rather than solely to satisfy a
non-nullable hook parameter.

An absent source has no active subscription. Execute the selector with the
source's absence value so the caller owns the resulting UI value. Preserve the
exact `null` and/or `undefined` allowed by the source type in the selector's
input, without adding absence to guaranteed sources. Real snapshots may also be
nullable; keep their types and subscriptions intact.

Infer the hook's return type from the selector. Put defaults in the selector,
not a separate `fallback` option. Compare selected results, including results
produced during source absence. A custom comparison must accept the selector's
whole result type without widening that result type.

Source retrieval must itself be reactive so React observes arrival, removal,
and replacement. Release subscriptions when sources change or the component
unmounts. Changing the selector or comparison should preserve the source
subscription. The source owner remains responsible for creating and disposing
the store; local state resets remain explicit in the consumer.

## Library Integration

Build on TanStack Store and Zustand's subscription and selection capabilities.
Keep application integration focused on source adaptation and the conventions
specified here, rather than maintaining another selection cache or subscription
engine. Prefer TanStack Store for new store-based capabilities; keep migrations
of existing Zustand or external-store models explicit and scoped.

Within each app or package source tree, use
`react/state/selectors/useSelector.ts` as the common selector entrypoint for
TanStack and migrated external stores. Delegate to TanStack's native
`useSelector`, adapting optional sources and legacy `getSnapshot`/unsubscribe
contracts with stable adapter identity. Keep snapshots referentially stable while the underlying value
is unchanged. Preserve the library's server-rendering and hydration contracts.

Existing Zustand integrations can retain their library implementation while
exposing the same selector and options conventions. Aligning a hook's signature
does not require changing the underlying store model.

Keep feature-specific selection visible at the call site:

```tsx
const count = useSelector(core.store, (state) => state.count);
const optionalCount = useSelector(core?.store, (state) => state?.count);
const items = useSelector(core?.store, (state) => state?.items ?? []);
const selection = useSelector(core.store, selector, {
  compare: customComparison,
});
```

## Equality Policy

Selector-based React subscriptions should default to `shallow` from
`zustand/shallow`, even when the source is not a Zustand store. Use `Object.is`
only for intentionally primitive-only APIs or explicit identity semantics.

Allow callers to provide a custom equality function when `shallow` is not
specific enough. Use custom equality for recurring derived shapes such as arrays
of objects, domain-specific snapshots, maps, sets, or normalized structures.

Keep reusable comparisons beside the common selector in
`react/state/selectors/equality-fns.ts`. Before adding a local comparison, check
whether the shared module already handles that shape. Add generally useful
comparisons there instead of leaving them embedded in one component.

```tsx
const rows = useSelector(store, (state) => state.rows, {
  compare: shallowObjArray_EqualityFn,
});
```

## Feature Surfaces

Keep raw reactive sources small and mechanical. Put feature-aware hooks and
imperative getters in the feature data layer or the nearest equivalent local
adapter.

Feature hooks should compose narrowly. Keep hooks that locate a source, resolve
an entity or adapter, derive domain data, or manage a feature lifecycle. Their
similar signatures are a useful convention, not duplicated subscription logic.
Consolidate repeated subscription, cache and comparison mechanics instead of
removing feature entrypoints solely because they delegate to a library hook.
When a derived convenience hook returns an array or object, choose an equality
that matches the returned shape.

Place required identifying inputs first, then the selector, then an optional
options object. Use `compare` for the optional comparison and default it to
`shallow`. Feature options extend the common selector options with concerns such
as `adapterIp`; the feature consumes those concerns before delegating. This keeps
selection, inference and comparisons consistent across source libraries.

Export `SelectorOptions<T>` from the common selector so feature hooks can extend
its options without redeclaring the comparison contract:

```ts
type EntityOptions<T> = SelectorOptions<T> & {
  adapterIp?: string;
};
```

```tsx
const label = useEntityById(entityId, (entity) => entity?.label, {
  adapterIp,
  compare: Object.is,
});
```

Source absence and a domain lookup miss are separate facts. A present store may
contain no matching entity; the feature selector still receives `undefined`.
A generic selector over a guaranteed source does not gain absence unless the
stored state itself permits it. Remove superseded generic selector
implementations and compatibility aliases when migrating their consumers.

Keep imperative getters separate from React subscriptions. Getters are direct
reads and do not need selector/equality parameters unless the local API has a
specific non-rendering reason.

## Review Procedure

When reviewing render-sensitive code, inspect each subscription:

- Identify the selected value and the value the component actually renders.
- Narrow the subscription when it selects more than the component needs.
- Move selectors into the subscription boundary when they run after a broad
  snapshot subscription.
- Add or expose an equality option when the selected value is not primitive or
  identity-stable.
- Default selector equality to `shallow`; use a custom equality when `shallow`
  is too broad or too narrow.
- Prefer a feature-level subscription surface before changing many consumers
  directly.

When an intervention aims to reduce unnecessary renders, follow the
[rendering verification criteria](verification.md), including their completion
requirements.
