# TanStack Store

For a new observable core that uses TanStack Store, expose its public store
as `store`. Follow [observable cores](../observable-cores.md) for the
observation contract and the relationship between the machine and its
public snapshot.

## React Adapters

When integrating TanStack Store or migrating a compatible external store to it,
delegate to TanStack's native `useSelector`. Adapt optional sources and legacy
`getSnapshot` and unsubscribe contracts with stable adapter identity. Keep
snapshots referentially stable while the underlying value is unchanged.
Preserve the library's server-rendering and hydration contracts.
