# Naming Standards

## Diagram Naming

Recommended pattern:

```text
<DeliverableID>-<AreaOrTopic>-<PrimaryLayer>-<Description>.<ext>
```

Examples:

```text
D0-enterprise-overview-L0-context.drawio
D1-datacenter-L2-segmentation.drawio
D1-campus-L2-segmentation.drawio
D1-remote-workers-L3-connectivity.drawio
D2-api-gateway-L5-service-architecture.drawio
```

## Object Prefixes

| Prefix | Family |
|---|---|
| AREA | Area object |
| ZONE | Network zone |
| ACTOR | Human or external actor |
| NET | Network infrastructure |
| SEC | Security control |
| IAM | Identity and access |
| APP | Application component |
| DATA | Data component |
| SVC | Shared service |
| FLOW | Connector / traffic flow |
| CONN | Connectivity / transport |

## Environment Values

| Value | Meaning |
|---|---|
| PROD | Production |
| NONPROD | Non-production, including Lab |
