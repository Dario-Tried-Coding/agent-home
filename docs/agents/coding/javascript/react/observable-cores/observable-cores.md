# React Observable Cores

When introducing or substantially redesigning an imperative core that supplies
observable state to React, use [TanStack Store](tanstack-store.md) for its
public snapshots and [XState](../../xstate.md) for its internal workflow. The core exposes
its own commands and lifecycle. XState owns workflow transitions, and the store
projects the observable state for consumers.

Use the store alone for state that has no workflow to model. Preserve existing
public contracts during scoped changes and plan store or machine migrations
explicitly.

Read [observable core contracts](../../../observable-cores.md) for state ownership,
snapshot identity, and verification. When React consumes the store, apply the
[React selector conventions](../optimization/rendering/subscriptions.md).
