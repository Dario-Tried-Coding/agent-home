# React Rendering Verification

Read this when implementing or reviewing work intended to reduce unnecessary
React renders, including selector migrations and subscription moves. These
criteria apply across apps and packages.

## Define The Claim

Before changing the implementation, identify the affected component or render
boundary, the update that causes unnecessary work, and the expected result.
Specify what must remain reactive as well as what should stop rendering.

Match the evidence to the claim. A synthetic hook consumer can establish that
hook's subscription isolation. When the claim concerns parent propagation,
Context, or a composed UI, exercise the actual component or composition at
that level. A hook-only test does not establish isolation of the whole page.

## Automated Regression

Add a permanent behavioral regression test. Demonstrate that it reproduces the
problem before the implementation change and passes afterward. Use real
reactive sources when their notification and selection behavior are part of
the problem.

Cover both sides of the contract:

- An irrelevant update preserves the rendered output and causes no additional
  renders through the affected path.
- A relevant update still reaches the UI and changes the expected output.

Measure deltas from a baseline after mount and initialization have settled.
Avoid absolute mount counts that depend on StrictMode or setup behavior. Flush
updates through the test environment's normal React integration before making
assertions.

State what the measurement counts. Source notifications, instance or selection
identity, render invocations, and React commits are different observations.
A counter in the component body counts render invocations; a React Profiler
callback observes commits. Fewer renders or commits alone do not demonstrate
less CPU work: a CPU claim needs a measurement of that work.

This self-contained example measures render invocations and UI behavior using
a real store. A broad `const { available } = useSource()` subscription in the
consumer would fail the irrelevant-update assertion:

```tsx
// @vitest-environment happy-dom
import { act, cleanup, render } from "@testing-library/react";
import { afterEach, expect, it } from "vitest";
import { create } from "zustand";

afterEach(cleanup);

it("ignores telemetry while keeping availability reactive", () => {
  const useSource = create<{ available: boolean; sequence: number }>(() => ({
    available: true,
    sequence: 0,
  }));
  let renders = 0;

  function Consumer() {
    const available = useSource((state) => state.available);

    renders++;

    return <button disabled={!available}>Add</button>;
  }

  const ui = render(<Consumer />);
  const button = ui.getByRole("button", { name: "Add" });
  expect(button.hasAttribute("disabled")).toBe(false);

  const baseline = renders;

  for (let sequence = 1; sequence <= 5; sequence++) {
    act(() => useSource.setState({ sequence }));
  }

  expect(renders - baseline).toBe(0);
  expect(button.hasAttribute("disabled")).toBe(false);

  act(() => useSource.setState({ available: false }));

  expect(renders).toBeGreaterThan(baseline);
  expect(button.hasAttribute("disabled")).toBe(true);
});
```

Use the owning scope's existing testing tools and store integration for actual
regressions. The example illustrates the two assertions, not a requirement to
introduce Zustand or a particular test library.

## Browser Verification

For a problem observed in the app, also verify the result before and after the
change in the browser under the representative conditions that produced it.
For other optimizations, browser verification is required when automated
evidence does not represent the promised behavior.

Account for differences between the test environment and the app, including
StrictMode and React Compiler configuration. Use a measurement that answers
the claim; update highlighting can locate activity but does not quantify CPU
cost. Preserve legitimate updates and unrelated activity while checking the
affected render path.

Report the scenario, measurement method, before/after result, and observation
duration or number of updates. Choose an observation window that includes the
triggering activity; there is no universal duration for every optimization.

## Review And Completion

Review the claimed result against the evidence at the affected boundary,
including both regression cases and browser verification when required. Remove
temporary diagnostic instrumentation before finishing; keep the regression test.

**Complete when:** the automated regression demonstrates the intended isolation
and retained reactivity, required browser verification confirms the result, and
the report identifies the metric, scenario, before/after observations, and any
remaining limitations.

If access, backend availability, or representative data prevents required browser
verification, report that verification as pending and identify what is missing.
Code and available evidence may still be delivered, but the completion criterion
remains unsatisfied until the required verification is performed.
