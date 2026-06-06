# ADR 0001: Use DCSA-compatible events as the canonical contract

## Status

Accepted

## Context

OpenTrackFlow needs a stable event contract for container tracking data. Defining a new shipping
data model would be unrealistic for a personal engineering project and would reduce credibility.

## Decision

Use DCSA-compatible event structures as the canonical contract.

Source-specific payloads should be converted by adapters. Core platform logic should depend on
normalized DCSA-compatible events, not source-specific formats.

## Consequences

Benefits:

- better credibility
- easier integration with real DCSA-compatible sources
- clearer system boundary
- less risk of over-designing a fake industry model

Trade-offs:

- the project must follow DCSA concepts
- some internal read models are still needed for query and timeline aggregation
