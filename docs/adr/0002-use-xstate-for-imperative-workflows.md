# Use XState for stateful workflows

Use XState by default when introducing or substantially redesigning a stateful
workflow in JavaScript or TypeScript, including workflows owned by imperative
cores. Explicit states, events, and guards make transitions easier to inspect
and edit visually. The core keeps its public commands, observation, and
lifecycle independent of the machine. When the core exposes an observable
store, we accept the cost of projecting machine state into the public snapshot.

Keep simple calculations and transformations direct. Preserve established
core interfaces during migration. See the [XState guidance](../agents/coding/javascript/xstate.md)
for implementation and verification.
