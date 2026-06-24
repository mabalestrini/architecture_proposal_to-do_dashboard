# Visio Stencil Construction Guide

## Purpose

This guide defines the baseline Visio stencil structure for professional network reference architecture diagrams.

## Stencil Groups

Create separate stencil sections or files for:

1. Trust Zones
2. Network Actors
3. Security Controls
4. Network Infrastructure
5. Network Services
6. Identity and Management
7. Applications and Platforms
8. Data Layer
9. Connectors and Flows
10. Diagram Annotations and Legends

## Master Shape Naming

Use the following naming convention:

```text
<FAMILY>_<OBJECT_TYPE>
```

Examples:

```text
ZONE_INTERNET
SEC_FIREWALL
SEC_WAF
IAM_PAM
APP_WEB
DATA_DATABASE
FLOW_TLS
```

## Shape Design Principles

- Use simple, recognizable shapes.
- Avoid excessive vendor-specific detail.
- Keep icons secondary to semantic shape meaning.
- Preserve readability at small sizes.
- Use restrained colors.
- Ensure shapes remain recognizable in grayscale.

## Recommended Shape Semantics

| Semantic | Shape |
|---|---|
| Trust zone | Rounded rectangle container |
| Application | Rounded rectangle |
| Security control | Hexagon or shield-like shape |
| Database | Cylinder |
| User / actor | Person icon or simple actor symbol |
| Cloud | Cloud outline |
| Flow | Connector with arrow |
| Cluster / HA | Double border or grouped objects |
| Logical object | Dashed border |
| Critical object | Thick border |

## Locking Rules

For master shapes:

- Lock aspect ratio.
- Lock theme overrides where needed.
- Keep text editable.
- Define connection points.
- Avoid allowing uncontrolled shape deformation.
- Use master shape updates instead of manual edits.

## Shape Data Fields

Recommended Visio shape data fields:

| Field | Type |
|---|---|
| ObjectID | String |
| ObjectType | String |
| Zone | String |
| Environment | String |
| Owner | String |
| Criticality | Enum |
| DataClassification | Enum |
| Technology | String |
| Notes | String |

For flows:

| Field | Type |
|---|---|
| Source | String |
| Destination | String |
| Protocol | String |
| Port | String |
| Purpose | String |
| Encryption | String |
| Authentication | String |
| Status | Enum |
