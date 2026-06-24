# Design Principles

## Repository Philosophy

The repository is an enterprise architecture diagram production system, not merely a collection of diagrams.

It supports:
- architecture governance
- diagram consistency
- AI-assisted generation
- automation
- reusable templates
- professional documentation

## Orientation Standard

Default orientation:
LEFT → RIGHT

Horizontal axis:
- exposure progression
- external-to-internal flow
- consumer-to-provider flow

Vertical axis:
- management/control hierarchy
- observability/security overlays
- infrastructure layering

Alternative orientations are allowed for:
- physical topology diagrams
- campus/floor layouts
- hierarchical diagrams
- protocol/layer models

## Diagram Scope

One diagram should mainly tell one story.

Avoid mixing:
- every IP
- every security control
- every application
- every transport detail
- every business flow

unless the diagram explicitly declares those as overlays.

## D0 Context Rule

D0 diagrams must show:
- areas
- actors
- macro relationships

D0 must not show by default:
- IPs
- VLANs
- routing detail
- firewall rules
- ports
- protocols
- detailed applications
- transport technologies

D0 may include L4 Security Control overlays on demand.
