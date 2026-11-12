# JSX

Use JSX for authored React trees in application code, packages, tests, and
fixtures. Use `.tsx` for TypeScript files that contain JSX.

## Children

Write children between the component's opening and closing tags:

```tsx
export function Actions() {
  return (
    <section>
      <button type="button">Save</button>
    </section>
  );
}
```

Keep render callbacks as nested JSX expressions. The component still receives
the function as its `children` prop and supplies its arguments when calling it:

```tsx
import type { ReactNode } from "react";

function Field({ children }: { children: (value: string) => ReactNode }) {
  return children("Ready");
}

export function Preview() {
  return <Field>{(value) => <span>{value}</span>}</Field>;
}
```

## Programmatic Construction

Reserve `createElement` for programmatic element construction where JSX would
obscure the operation, such as an API assembling elements from component types
and props supplied as data. A component selected at runtime can still be
rendered with JSX through a capitalized variable, such as `<Component />`.

Apply this convention to new and modified rendering code. Existing uses do not
require a repository-wide migration.
