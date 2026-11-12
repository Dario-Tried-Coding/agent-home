# React Contexts

Read this when defining, changing, or reviewing React Contexts, their Providers,
or access hooks. These conventions apply across apps and packages.

## Module Shape

For each Context exposed to consumers, keep these definitions together in this
order:

1. The Context value type.
2. The Context definition.
3. The Provider.
4. The access hook, followed by any additional access hooks.

Give distinct Contexts distinct files. When several Contexts belong to the same
responsibility, group their files under its `context/` directory. Keep each
Context with its owning responsibility rather than collecting unrelated
Contexts in a global folder.

A Provider that only coordinates effects may have no Context to expose. A
component that composes existing Providers may have no Context of its own.
Include only the elements that the responsibility actually needs.

## Required And Optional Access

The ordinary access hook requires its Provider and throws a descriptive error
when that Provider is missing. Use a dedicated absence sentinel such as `null`
or `undefined`, and compare against it explicitly when valid values can be
falsy. Name the hook and required Provider in the error.

An optional access hook is appropriate only when a missing Provider is an
intentional part of the consumer contract. Make that optionality explicit in
its name and return type, and document what absence means. Keep required access
strict when both forms exist.

Distinguish a missing Provider from an absent data source inside a valid
Provider. For example, an editor can have no document selected while still
requiring its Provider. Represent that lifecycle state in the Context value;
reserve the outer sentinel for the missing Provider.

```tsx
import { createContext, useContext, type PropsWithChildren } from "react";

export type Editor_Context = {
  documentId: string | null;
};

const Editor_Context = createContext<Editor_Context | undefined>(undefined);

export function Editor_Provider({
  value,
  children,
}: PropsWithChildren<{ value: Editor_Context }>) {
  return <Editor_Context value={value}>{children}</Editor_Context>;
}

export function useEditor_Context() {
  const ctx = useContext(Editor_Context);

  if (ctx === undefined)
    throw new Error("useEditor_Context must be used within Editor_Provider");
  return ctx;
}
```

The example's `documentId: null` is a valid value under the Provider, not a
missing Provider. Keep the guard and successful return together as described in
[readability](../code-quality/typescript-readability.md#guards-and-returns).

For Context value identity and reactive updates, follow
[rendering subscriptions](optimization/rendering/subscriptions.md#context-and-core-handles).
For ownership and disposal of resources carried by Context, follow
[imperative lifecycles](imperative-lifecycles.md).
