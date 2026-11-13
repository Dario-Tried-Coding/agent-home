# React Repository Organization

Organize feature-owned code under `src/features/`. Give application subsystems
that have their own responsibilities sibling directories under `src/` as needed.

```text
src/
├── features/           # feature-owned behavior and UI
└── data/               # example of an application subsystem
```

When a feature needs to extend an existing subsystem, place the extension in
that subsystem by default, close to the component that uses it and governs its
lifecycle. This also applies when the extension serves a single feature.
