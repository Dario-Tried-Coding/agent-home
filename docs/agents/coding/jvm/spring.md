# Spring

## Boundaries

Keep controllers thin. Put business decisions in services, guards, validators,
runtime stores, or command helpers according to the existing package pattern.

## Spring Components

Prefer constructor injection for Spring-managed components and their long-lived
service dependencies. When Spring owns activation or shutdown, put that work in
the applicable lifecycle hooks. Keep request-specific context with the operation
that uses it.

## REST And Validation

Keep REST DTO validation at the API boundary, and keep compatibility decisions
close to the validator or catalog that owns them.
For Java DTOs, also follow [Java type ownership and validation](java.md).

## Runtime Profiles

Preserve runtime profile boundaries. A setting belongs in a profile-specific
configuration file only while it diverges by environment or exists for one
profile-specific need; shared conventions belong in `application.yml`.

## Configuration And Test Scenarios

Trace operational settings from their authoritative configuration source through
binding to the observable consumer. Preserve Spring property precedence and
intentional fallbacks; verify explicit overrides reach the behavior they control.
Keep protocol constants and product invariants with their owning capability.

Behavioral tests declare the configuration values their scenarios assume,
independently of deployment files. Dedicated configuration tests use non-default
overrides and independently specified expected outcomes to exercise binding and
behavior together. Cover affected runtime profiles when their composition differs.
