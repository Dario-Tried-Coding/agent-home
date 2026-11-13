# React State And Shape

Read this when writing or reviewing React state ownership, component props and
forwarding, component shape, or hook placement.

## Component Props

For a UI component with an identifiable public root, derive its supported props
from the underlying DOM element or primitive, for example with `ComponentProps`,
and spread the remaining props onto that root. Consume domain props separately.

Expose standard DOM attributes through their native names, including `data-*`
and `aria-*`, so callers can pass attributes such as `{...tId(...)}` directly
to the component. A domain identifier remains a domain prop; an attribute used
only for DOM instrumentation belongs to the root's attribute contract.

The public root is the intended attribute target, which may differ from the
outermost DOM node. Preserve established destinations when a component routes
`className` to a layout container and other attributes to an inner primitive.

Compose caller classes, styles, event handlers, and refs with behavior the
component owns. Express intentional restrictions in its supported props, for
example with `Omit`, and explain exclusions whose reason is not evident from
the component's responsibility.

For providers, fragments, portals, or components with multiple roots, define a
meaningful target or slot contract when callers need attributes. Keep the
rendered structure appropriate to the UI rather than adding a wrapper solely
to provide a forwarding destination.

## Component-Local Hooks

Keep a hook private in the component file when its job is to organize that
component's logic and its lifecycle follows the component. Give the hook its
own module when it owns an independent responsibility or serves multiple
callers.

## State Management

### React Is An Adapter

Treat React APIs as rendering integration tools, not as the default substrate
for application state.

Use component-local React state for genuinely local UI affordances: open/closed
controls, temporary input affordances, focus-like interaction state, and values
whose lifetime is naturally the component instance.

Before reaching for `useState`, `useEffect`, or `useRef`, check whether the
state belongs in an existing store, query cache, form state, router state,
imperative observable model, or domain service.

### Model State Explicitly

If multiple booleans, nullable values, refs, or effects together describe one
lifecycle, model that lifecycle directly. Prefer a discriminated union,
finite-state model, store state, or another explicit representation that makes
invalid combinations unrepresentable.

Do not use a cluster of component-local hooks as an implicit state machine for
hydration, persisted baselines, retries, autosave, conflict handling, or other
application workflows.

### Keep React Thin

React components should adapt state to rendering and user events. Put domain
behavior, lifecycle transitions, and cross-component state in the app's existing
state and data layers.

When a React component coordinates an external state model, keep it as a thin
adapter: subscribe through selectors, dispatch actions, and render the selected
state.
