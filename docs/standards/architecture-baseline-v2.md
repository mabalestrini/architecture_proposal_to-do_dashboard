# Network Reference Architecture — Baseline V2

| Field | Value |
|---|---|
| Version | 2.0-draft |
| Status | Draft for review |
| Date | 2025-06-02 |
| Owner | Architecture |
| Replaces | Baseline V1.0 (network_reference_architecture), network_reference_diagrams V1 |

---

## Table of contents

1. [Purpose and scope](#1-purpose-and-scope)
2. [Architecture model — core concepts](#2-architecture-model--core-concepts)
3. [Areas](#3-areas)
4. [Zones](#4-zones)
5. [Zone catalog](#5-zone-catalog)
6. [Actors](#6-actors)
7. [Connectivity baseline](#7-connectivity-baseline)
8. [Security platform](#8-security-platform)
9. [Color standards](#9-color-standards)
10. [Connector and line-style standard](#10-connector-and-line-style-standard)
11. [Layer model and overlay rule](#11-layer-model-and-overlay-rule)
12. [Diagram governance](#12-diagram-governance)
13. [Object catalog](#13-object-catalog)
14. [Tooling, naming and workflow](#14-tooling-naming-and-workflow)
15. [Deliverables roadmap](#15-deliverables-roadmap)
16. [Decisions log](#16-decisions-log)
17. [Changelog](#17-changelog)

---

## 1. Purpose and scope

This document is the authoritative reference baseline for the enterprise network reference architecture programme. It defines the model, standards, and conventions that govern all diagram production, documentation, and tooling decisions.

**What this document governs:**
- The conceptual model (Areas, Zones, Actors, Environments, Exposure)
- Color palette for areas and zones
- Connector and line-style conventions
- Layer model and overlay rule
- Diagram governance rules
- Object catalog and naming prefixes
- Tooling, file naming, and workflow
- Deliverables roadmap (Scopes 1, 2, 3)

**What this document does not govern:**
- Individual diagram content or business flows (covered by per-deliverable narratives)
- Network device configurations
- Security policy rulebases

**Repository consolidation note:** This document merges and supersedes content from two prior repositories: `network_reference_architecture` (standards and deliverable templates) and `network_reference_diagrams` (diagram assets, object catalog, zone CSV, mermaid templates). Where the two sources conflict, this document is authoritative.

---

## 2. Architecture model — core concepts

Five orthogonal dimensions. They describe different aspects of the same infrastructure and must never be mixed or conflated.

| Concept | Definition | Answers |
|---|---|---|
| **Area** | Major operational or physical domain of the enterprise. | Where does it live? |
| **Zone** | Security-segmented network segment inside an Area, isolated by firewalls, VRFs, ACI EPGs or routing controls. | How is it protected? |
| **Actor** | Person, user group, or external entity that consumes, administers, or interacts with services. | Who interacts with it? |
| **Environment** | Operational lifecycle classification. Baseline: PROD and NONPROD. | Production or non-prod? |
| **Exposure** | Reachability and attack-surface posture. Not business criticality. Not implicit trust. | How reachable is it? |

**Critical separations:**
- Areas are large operational domains. Zones are segmented network segments *inside* Areas.
- Actors live in or access Areas, but they are not Areas.
- MGMT/OOB is a Zone, not an Area.
- Lab is a Zone (NONPROD) due to its dedicated isolation and segmentation controls.
- Company Cloud is company-managed but not automatically trusted.

---

## 3. Areas

Five approved Areas. Area colors identify operational domains across all diagrams. Colors are soft semantic cues — pastel, low-saturation, print-friendly.

| Area | Definition | Fill | Border |
|---|---|---:|---:|
| **Internet** | Public uncontrolled network. Customers and Remote Workers originate here. | `#ECECEC` | `#B0B0B0` |
| **External Networks** | Third-party company networks via VPN, leased lines, partner links, or private connectivity. Not the same as Internet. | `#EFE2D0` | `#C9B79C` |
| **Campus** | Company offices, HQ, branches, and facilities. Employees consume services over wired LAN or WiFi. | `#DDE7D8` | `#A9B89F` |
| **Data Center** | Company-owned on-prem infrastructure. DC1 and DC2 are mirrored; both operational, one active, switchover every 6 months. | `#DDECCF` | `#98B27A` |
| **Company Cloud** | Company-managed Azure infrastructure. Two mirrored regions. Connected to DCs via ExpressRoute. Not automatically trusted. | `#DCE7F5` | `#9EB6D8` |

**D0 rule:** D0 diagrams show DC1 and DC2 as separate physical entities. D1+ may abstract them as one DC where mirrored duplication adds no architectural value.

---

## 4. Zones

A Zone is a security-segmented network segment inside an Area. Zone colors indicate **exposure posture**, not business criticality.

### Exposure ladder

| Exposure level | Color direction | Fill | Border |
|---|---|---:|---:|
| Low | Green family — most protected | `#DDECCF` | `#98B27A` |
| Medium | Yellow family | `#F5EACF` | `#D8BF7A` |
| High | Orange family — most exposed | `#F3D7C8` | `#D69A7A` |
| Administrative | Lavender — privileged mgmt plane | `#E8DDF0` | `#B59BC7` |
| Controlled NONPROD | Mint — lab / isolated testing | `#DDF0E8` | `#9CC6B2` |

**Zone principles:**
- Zones encode exposure posture — not business criticality and not implicit trust.
- Color is a soft cue. Pastel palette. No SOC-style alarm visuals.
- Zone isolation is enforced by firewalls, VRFs, Cisco ACI EPGs, and routing controls.
- The zone catalog is extensible. Additional zones may be defined per deliverable.
- Saturated red is reserved for temporary or exception conditions only — never a permanent zone.
- All diagrams must remain readable in grayscale.

---

## 5. Zone catalog

Baseline zone definitions. The Zone_ID column is the canonical identifier for diagram automation and CSV-based workflows.

| Zone | Zone_ID (canonical) | Exposure | Fill | Border | Definition | Typical workloads |
|---|---|---|---:|---:|---|---|
| **DMZ** | DMZ | High | `#F3D7C8` | `#D69A7A` | Protective buffer between internal systems and external networks. Hosts traffic termination and edge services. | Load balancers, reverse proxies, VPN gateways, WAF, public DNS, partner gateways |
| **Restricted** | REZ | Medium | `#F5EACF` | `#D8BF7A` | Services consumed by external users or partners under controlled access. Intermediate layer between DMZ and Trusted Internal. | API mgmt, federation, partner communication, controlled service layers |
| **Trusted Internal** | INT | Low | `#DDECCF` | `#98B27A` | Core enterprise services. Highest protection posture. Limited direct exposure. | Front-end, middleware, back-end, databases, internal services |
| **Guest** | GUEST | Medium / High | `#F3E2D6` | `#D7A88C` | Isolated access for guests, contractors, or temporary users. Strict isolation from corporate resources. | Guest WiFi SSID, internet-only break-out, captive portal |
| **Facility** | FAC | Medium | `#EADCBF` | `#C6A56D` | Building and operational systems — access control, CCTV, sensors, clocks, intercoms, IoT. | BMS, CCTV, badge readers, IoT sensors, smart-locks |
| **MGMT / OOB** | MGMT | Administrative | `#E8DDF0` | `#B59BC7` | Administration, monitoring, recovery, and infrastructure control. Not an Area — an isolated zone. Dual name retained for DORA-style resilience vocabulary. | Jump hosts, console servers, IPMI/iLO, NMS, backup, PAM |
| **Lab** | LAB | Controlled NONPROD | `#DDF0E8` | `#9CC6B2` | Logically separated non-production test laboratory. NONPROD from an infrastructure perspective. | Equipment validation, configuration testing, architecture validation |

### Extended zone instances (from zone_catalog.csv)

Key named zones derived from the zone catalog. These appear in detailed D1/D2 diagrams.

**Data Center — DMZ zone instances:**

| Zone_ID | Zone name | Notes |
|---|---|---|
| DMZ_INTERNET_LB | DMZ Internet LB | Internet-facing load balancer |
| DMZ_INTERNET_VPN | DMZ Internet VPN | VPN termination for remote access |
| DMZ_INTERNET_XFB | DMZ Internet XFB | Cross-functional boundary / integration |
| DMZ_INTERNET_DNS | DMZ Internet DNS | Public DNS |
| DMZ_PORTIMA | DMZ Portima | Partner / service exposure |
| DMZ_BNPPF | DMZ BNPPF | BNPPF connectivity |
| DMZ_BLOOMBERG | DMZ Bloomberg | Bloomberg connectivity zone |

**Data Center — Restricted zone instances:**

| Zone_ID | Zone name | Notes |
|---|---|---|
| REZ_ANONYMOUS | REZ Anonymous | Anonymous external services |
| REZ_AUTHENTICATED | REZ Authenticated | Authenticated external services |
| REZ_MGMT | REZ-MGMT | Restricted management access |
| REZ_CONAC | REZ-Conac | Controlled application connectivity |
| REZ_ADFS | REZ-ADFS | Identity federation (ADFS) |
| REZ_INFRA | REZ-Infra | Infrastructure services (DNS, NTP, DHCP) |

**Data Center — Trusted Internal zone instances:**

| Zone_ID | Zone name | Notes |
|---|---|---|
| REMEDIATION | Remediation | Patch and remediation systems |
| FTA | FTA | File transfer / acceleration |
| MAGICS_DEV | MAGICS Dev | Development environment |
| MAGICS_QUAL | MAGICS Qual | Quality assurance |
| MAGICS_ACC | MAGICS Acc | Acceptance |
| MAGICS_PROD | MAGICS Prod | Production |

**Campus zones:**

| Zone_ID | Zone name | Exposure |
|---|---|---|
| TRUST_USERS | Trust Users | Low |
| TRUST_PRINTERS | Trust Printers | Low |
| TRUST_LOCAL_SERVERS | Trust Local Servers | Low |
| FACILITY_CLOCKS | Facility Time Clocks | Medium |
| FACILITY_ROOM_MGMT | Facility Room Management | Medium |
| FACILITY_SECURITY | Facility Security Devices | Medium |
| GUEST_EXTERNAL | External Guests | High |
| GUEST_INTERNAL | Internal Guests | Medium |
| GUEST_IOT_UC | IoT / UC Guest | Medium |

**Company Cloud zones:**

| Zone_ID | Zone name | Exposure |
|---|---|---|
| CLOUD_DMZ_PUBLIC | Cloud Public Services | High |
| CLOUD_RESTRICTED | Cloud Restricted Services | Medium |
| CLOUD_INTERNAL | Cloud Internal Services | Low |
| CLOUD_MANAGEMENT | Cloud Management Plane | Administrative |
| CLOUD_EXPRESSROUTE | ExpressRoute Connectivity | Controlled |
| REMOTE_AVD | Azure Virtual Desktop (AVD) | Controlled |

---

## 6. Actors

Actors consume, administer, or interact with services. They are not Areas and not Zones.

| Actor | Description | Typical location |
|---|---|---|
| Customers / Clients | Consume public-facing company services. | Internet |
| Partners | External organisations connected through External Networks. | External Networks |
| Campus Workers | Employees physically present in company offices using wired LAN or corporate WiFi. | Campus |
| Remote Workers | Employees outside company facilities using Prisma VPN or VDI/AVD. Physical endpoint may be unmanaged (VDI path). | Internet |
| Administrators | Infrastructure, platform, and security administrators. | Campus and Internet |
| Guests | Non-employees requiring temporary connectivity, typically Guest WiFi. | Campus |

**Remote Workers — two models:**

| Model | Endpoint control | Workspace control |
|---|---|---|
| Prisma VPN + corporate laptop | Company-managed physical device | Company-managed local workspace |
| VDI / AVD | Possibly unmanaged physical device | Company-controlled virtual workspace |

---

## 7. Connectivity baseline

### Data Center pair

| Attribute | Baseline |
|---|---|
| Data centers | DC1 and DC2 |
| Design | Mirrored |
| Operating model | Both operational; one active at a given time |
| Switchover | Every 6 months |
| Purpose | DRP validation; assurance both sites remain true mirrors |

### Inter-DC transport (DWDM, dual-provider)

| Traffic domain | Provider | Capacity | Technology | Purpose |
|---|---|---|---|---|
| WKS | Orange | 2×10 Gb | DWDM | Workstation / user service interconnect |
| WKS | Arcadiz | 2×10 Gb | DWDM | Workstation / user service interconnect |
| DATA | Orange | 3×10 Gb | DWDM | Application / server / data interconnect |
| DATA | Arcadiz | 3×10 Gb | DWDM | Application / server / data interconnect |
| SAN | Orange | 8×8 Gb | DWDM | FC SAN / storage replication fabric |
| SAN | Arcadiz | 8×8 Gb | DWDM | FC SAN / storage replication fabric |
| INT | Orange | 10 Gb | DWDM | Internal infrastructure interconnect |
| INT | Arcadiz | 10 Gb | DWDM | Internal infrastructure interconnect |

Orange and Arcadiz provide carrier diversity. WKS, DATA, SAN, and INT are separated transport domains.

### Campus and branch connectivity

| Connection | Technology |
|---|---|
| Domestic offices / branches to data centers | Redundant MPLS |
| Offshore offices to data centers | Cisco Meraki |

### Company Cloud connectivity

| Element | Baseline |
|---|---|
| Cloud provider | Azure |
| Azure regions | 2 mirrored regions |
| DC to Azure | ExpressRoute |
| Design implication | Hybrid diagrams show both DC pair and Azure region pair |

### Topology summary

```
Campus / Branches
  |-- domestic: redundant MPLS
  |-- offshore: Cisco Meraki
  |
DC1  <-- DWDM dual-provider (WKS / DATA / SAN / INT) -->  DC2
  |                                                          |
ExpressRoute                                         ExpressRoute
  |                                                          |
Azure Region 1  <--------- mirrored --------->  Azure Region 2
```

---

## 8. Security platform

### Firewall technologies

| Technology | Role |
|---|---|
| Palo Alto | Firewalling, segmentation, security enforcement |
| Check Point | Firewalling, segmentation, security enforcement |

### Remote access and web proxy (Prisma)

| Function | Description |
|---|---|
| VPN | Remote worker VPN access using company-managed laptops |
| Secure Web Proxy | Cloud-hosted web proxy for Internet access governance and enforcement |
| ZTNA | Zero-trust network access (in scope for D1-REMOTE / D2 refresh) |

Prisma must appear in: Remote Workers diagrams, Internet access diagrams, Security Controls overlays, D1/D2 security views.

### Data center fabric

| Component | Technology |
|---|---|
| Fabric | Cisco ACI (spine-leaf) |
| Policy model | VRFs, EPGs, ACI policy model |
| Compute | Hyper-V clusters + physical servers |
| Domain controllers | Physical (not virtualised) |
| Server tiers | Front-end, middleware, back-end |

---

## 9. Color standards

### Principle

Color is a **soft semantic cue**, not an alarm system.

- Use pastel, low-saturation colors.
- Avoid saturated red for permanent zones (reserved for temporary/exception annotation only).
- No SOC-style alarm palette.
- Diagrams must remain readable when printed in grayscale.
- Avoid vendor branding colors as semantic colors.

### Area color palette (locked)

| Area | Fill | Border |
|---|---:|---:|
| Internet | `#ECECEC` | `#B0B0B0` |
| External Networks | `#EFE2D0` | `#C9B79C` |
| Campus | `#DDE7D8` | `#A9B89F` |
| Data Center | `#DDECCF` | `#98B27A` |
| Company Cloud | `#DCE7F5` | `#9EB6D8` |

### Zone exposure color palette (locked)

| Zone | Fill | Border |
|---|---:|---:|
| DMZ | `#F3D7C8` | `#D69A7A` |
| Restricted | `#F5EACF` | `#D8BF7A` |
| Trusted Internal | `#DDECCF` | `#98B27A` |
| Guest | `#F3E2D6` | `#D7A88C` |
| Facility | `#EADCBF` | `#C6A56D` |
| MGMT / OOB | `#E8DDF0` | `#B59BC7` |
| Lab | `#DDF0E8` | `#9CC6B2` |

### Color coding rationale

The palette is an industry-informed semantic visualization model, not a formal standard. It is informed by network/security diagram conventions, enterprise architecture practice, and UI/UX principles.

**Interpretation hierarchy (most to least important):**
1. Layout and spatial arrangement
2. Zone containers and boundaries
3. Shape semantics (cylinder = DB, hexagon = security control, etc.)
4. Connector semantics (line style and color)
5. Labels (explicit text always wins)
6. Color (supporting cue only)

**Semantic color guide for object families:**

| Semantic | Color direction | Object families |
|---|---|---|
| External / uncontrolled | Gray | Internet, unknown systems |
| External Networks | Warm beige | Partner networks |
| Security controls | Red / orange accent | Firewalls, WAF, enforcement |
| Identity / management | Purple / lavender | IAM, PAM, MGMT/OOB |
| Internal application | Green family | App services, Trusted zones |
| Data layer | Teal | Databases, storage |
| Cloud | Blue | Company Cloud, Azure services |
| Transport / connectivity | Blue-gray | WAN, DWDM, MPLS, ExpressRoute |
| Warning / exception | Amber | Temporary flows, review-required |
| Prohibited / critical | Red | Insecure paths, forbidden flows |

---

## 10. Connector and line-style standard

### Connector types

| Flow type | Color | Pattern | Weight | Use |
|---|---|---|---|---|
| **Data plane** | `#444444` | Solid | 1.5 pt | Default application / user traffic |
| **Mgmt / control** | `#7A5BA8` | Dashed 6,3 | 1.2 pt | Admin access, NMS, control plane |
| **OOB** | `#7A5BA8` | Dotted 2,3 | 1.2 pt | Console, IPMI/iLO, OOB network |
| **Encrypted (VPN/TLS)** | `#2E6B4F` | Solid | 1.5 pt | IPsec, TLS, MACsec, ExpressRoute |
| **Partner / 3rd party** | `#8B6A3F` | Dash-dot | 1.5 pt | External Networks ↔ DC |
| **Replication (DC↔DC)** | `#444444` | Double / 2.5 pt | 2.5 pt | DWDM, SAN, data replication |
| **Temporary / exception** | `#B05050` | Dashed 4,4 | 1.2 pt | Must include expiry date label |

### Flow label format

```
Standard:  Protocol / Port / Purpose
Encrypted: Protocol / Port / Purpose / Auth+Enc
Temporary: Protocol / Port / Purpose / Valid until YYYY-MM-DD
```

Examples:
```
HTTPS / 443 / User portal access
OIDC over TLS / 443 / Authentication / TLS 1.3
IPsec IKEv2 / 500+4500 / VPN tunnel / Valid until 2025-09-01
```

### Object shape conventions (Draw.io / Visio)

| Semantic | Shape | Notes |
|---|---|---|
| Zone container | Rounded rect, fill = zone color | Label at top-left inside |
| Security control | Hexagon or thick-border rect | Orange/red color family |
| Server / compute | Plain rectangle | Color = zone color |
| Database | Cylinder | Teal color family |
| Actor / user | Person icon or diamond | Neutral gray |
| Cloud service | Pill (high border-radius) | Azure blue family |
| HA / clustered | Double border | Same shape as primary |
| Deprecated | Dashed border, gray fill | Include retirement target date |

---

## 11. Layer model and overlay rule

### Layer catalog

| Layer | Name | Purpose |
|---|---|---|
| L0 | Context | Areas, actors, and macro relationships |
| L1 | Infrastructure topology | Physical and logical infrastructure domains |
| L2 | Network segmentation | Zones, VRFs, EPGs, VLANs, routing domains |
| L3 | Connectivity | MPLS, Meraki, ExpressRoute, DCI, VPN, leased lines |
| L4 | Security controls | Firewalls, WAF, Prisma, NAC, IDS/IPS, enforcement |
| L5 | Service architecture | Applications, APIs, file transfer, databases, platforms |
| L6 | Identity and access | IAM, MFA, federation, conditional access, PAM |
| L7 | Resilience and operations | DR, HA, replication, monitoring, backup, switchover |

### Overlay rule

Every diagram declares:

```
Primary Layer: Lx
Optional Overlays: Ly, Lz (if any)
```

Security overlays (L4, L6) may be required on non-security diagrams for regulatory compliance even when the diagram's primary purpose is infrastructure topology or connectivity.

### D0 context rule

D0 diagrams show Areas, Actors, and macro relationships only.

D0 must **not** include by default:
- IP addresses or subnets
- VLANs
- Routing detail
- Firewall rules
- Ports or protocols
- Detailed applications
- Transport technology specifics

D0 **may** include L4 Security Control overlays when explicitly declared.

---

## 12. Diagram governance

### Core rules

1. One diagram tells one story.
2. Every diagram has a clear title and stated scope.
3. Every diagram declares a Primary Layer and any Overlays.
4. Areas, Zones, and Actors are clearly and separately identified.
5. Boundary crossings are explicit in L2+ diagrams.
6. Security controls sit on or near enforcement boundaries.
7. Administrative flows are visually distinct.
8. Temporary or exception flows are dashed and dated.
9. Deprecated components are gray or dashed with retirement date.
10. Diagrams remain readable in grayscale.

### Diagram types

| Type | Primary layer | Purpose |
|---|---|---|
| Context diagram | L0 | System boundary and external actors |
| Infrastructure overview | L1 | Physical and logical topology |
| Trust-zone diagram | L2 | Zones and boundary controls |
| Connectivity diagram | L3 | WAN, MPLS, VPN, cloud links |
| Security control diagram | L4 | Inspection, enforcement, monitoring |
| Application / service diagram | L5 | Apps, APIs, data flows |
| Identity and access diagram | L6 | IAM, MFA, PAM flows |
| Resilience / DR diagram | L7 | HA, DR, replication, OOB |

### Deliverable rule

**A diagram alone is not a complete deliverable.** Every diagram is paired with a structured narrative following the architecture document template:

| Section | Purpose |
|---|---|
| Objective | Why this view exists |
| Scope | What is included and excluded |
| Primary Layer | Main layer represented |
| Optional Overlays | Additional layers |
| Areas | Areas represented |
| Actors | Actors represented |
| Zones | Zones represented |
| Connectivity | Major connectivity patterns |
| Security Controls | Enforcement points |
| Resilience | HA / DR characteristics |
| Assumptions | Design assumptions |
| Risks | Known risks |
| Open Items | Items for validation |

---

## 13. Object catalog

### Naming convention

```
<PREFIX>_<OBJECT_TYPE>[_<INSTANCE>]
```

| Prefix | Family | Example |
|---|---|---|
| `AREA` | Area container | `AREA_DATACENTER` |
| `ZONE` | Zone container | `ZONE_DMZ` |
| `ACTOR` | Actor | `ACTOR_REMOTE_WORKER` |
| `NET` | Network infrastructure | `NET_ROUTER` |
| `SEC` | Security control | `SEC_FIREWALL` |
| `IAM` | Identity and access | `IAM_PAM` |
| `APP` | Application component | `APP_WEB_PORTAL_01` |
| `DATA` | Data component | `DATA_DATABASE` |
| `SVC` | Shared service | `SVC_DNS` |
| `FLOW` | Connector / flow | `FLOW_TLS_443` |
| `CONN` | Connectivity / transport | `CONN_EXPRESSROUTE` |

### Security controls

| Object | Shape | Notes |
|---|---|---|
| `SEC_FIREWALL` | Hexagon | Boundary enforcement |
| `SEC_WAF` | Hexagon | Before web / API |
| `SEC_IDS_IPS` | Hexagon | Inline or monitoring path |
| `SEC_PROXY` | Hexagon | Label direction (forward / reverse) |
| `SEC_VPN_GATEWAY` | Hexagon | Label tunnel type |
| `SEC_SIEM` | Rounded rect | Receives logs/events |
| `SEC_EDR` | Rounded rect | Agent / service |

### Identity and access

| Object | Shape | Notes |
|---|---|---|
| `IAM_DIRECTORY` | Cylinder | AD, LDAP, Entra ID |
| `IAM_IDP` | Rounded rect | SAML/OIDC provider |
| `IAM_PAM` | Rounded rect (dark border) | High-sensitivity |
| `IAM_MFA` | Rounded rect | Attached to login flows |
| `IAM_BASTION` | Rounded rect | Admin jump host |

### Network infrastructure

| Object | Notes |
|---|---|
| `NET_ROUTER` | Label routing domain |
| `NET_SWITCH` | Avoid excessive physical detail |
| `NET_LOAD_BALANCER` | Show VIP / service name |
| `NET_SD_WAN` | Show underlay/overlay if needed |
| `NET_ACI_SPINE` | Cisco ACI spine |
| `NET_ACI_LEAF` | Cisco ACI leaf |

### Shared services

| Object | Notes |
|---|---|
| `SVC_DNS` | Label internal / external |
| `SVC_DHCP` | Supporting service |
| `SVC_NTP` | Important for security/logging |
| `SVC_PKI` | Certificate trust anchor |
| `SVC_MONITORING` | Metrics / log collection |
| `SVC_BACKUP` | Label RPO/RTO when relevant |

### Application and data components

| Object | Shape | Notes |
|---|---|---|
| `APP_WEB` | Rounded rect (green) | Usually receives HTTPS |
| `APP_API` | Rounded rect (teal) | Label API protocol |
| `APP_MIDDLEWARE` | Rounded rect | Integration layer |
| `APP_QUEUE` | Rounded rect (yellow-green) | Async flow |
| `APP_VM` | Plain rect | Platform-level |
| `APP_CONTAINER` | Rounded rect (mint) | Containerised service |
| `DATA_DATABASE` | Cylinder (teal) | Use cylinder shape |
| `DATA_STORAGE` | Rect (teal) | Label type |
| `DATA_CACHE` | Rounded rect (light teal) | Redis / memory cache |

### Object metadata minimum fields

**For objects:**

| Field | Example |
|---|---|
| id | `APP_WEB_PORTAL_01` |
| name | Customer Portal |
| type | `APP_WEB` |
| zone | `ZONE_DMZ` |
| environment | PROD |
| owner | Application Team |
| criticality | HIGH |
| data_classification | Confidential |
| technology | NGINX |

**For flows:**

| Field | Example |
|---|---|
| source | `ACTOR_END_USER` |
| destination | `APP_WEB_PORTAL_01` |
| protocol | HTTPS |
| port | 443 |
| purpose | User access |
| encryption | TLS 1.3 |
| authentication | OIDC |
| status | Approved |

---

## 14. Tooling, naming, and workflow

### Tool chain

| Purpose | Tool |
|---|---|
| Primary diagram editing | Draw.io / diagrams.net |
| Structural draft language | Mermaid (AI-friendly, version-controllable) |
| Professional high-detail diagrams | Visio (reserved) |
| Source control | GitHub |
| Publishing / presentation | PowerPoint (.pptx) |
| Theme and master | Corporate `.thmx` / Slide Master |

### Recommended workflow

```
AI → Mermaid → Draw.io → PPT (Visio only when required)
```

Mermaid is an intermediate representation and AI generation language — not the final rendering engine.

Draw.io is a drafting and prototyping platform. The output is used as:
- A topology scaffold
- An object and flow inventory
- A conversation artifact

PowerPoint is the publishing format. Diagrams are exported from Draw.io as high-resolution PNG (transparent background, @2×, 1920 px wide) and embedded as full-bleed images. Slide carries title + legend strip only.

### Slide frame standard

Every slide uses:
- **Section tag** — 9 pt gray, spaced caps (e.g., `DEFINITIONS`, `STANDARDS`)
- **Title** — 22 pt, Calibri Light or equivalent heading font
- **Footer** — document name (8 pt, bottom-left), slide number (8 pt, bottom-right)
- **No decorative accent lines** under titles
- **Neutral white background** — company Slide Master applied on top

### File naming conventions

```
<DeliverableID>-<AreaOrTopic>-<PrimaryLayer>-<Description>.<ext>
```

Examples:
```
D0-enterprise-overview-L0-context.drawio
D1-datacenter-L2-segmentation.drawio
D1-campus-L2-segmentation.drawio
D1-remote-workers-L3-connectivity.drawio
D2-api-gateway-L5-service-architecture.drawio
```

### Draw.io layer convention

Within each Draw.io file, use separate layers:

| Layer | Contents |
|---|---|
| ZONES | Zone container rectangles |
| DEVICES | Infrastructure, app, and data objects |
| CONNECTIONS-data | Data plane connectors |
| CONNECTIONS-mgmt | Management / control plane connectors |
| CONNECTIONS-oob | OOB connectors |
| LABELS | Annotations, flow labels |

### Repository structure

```
/diagrams/drawio/         — source .drawio files
/diagrams/mermaid/        — mermaid source files
/diagrams/exports/png/    — exported PNGs for embedding
/diagrams/exports/pdf/    — PDF exports
/docs/architecture-baseline/  — model and standards
/docs/deliverables/       — per-deliverable narrative docs
/docs/standards/          — this baseline + sub-standards
/schemas/                 — YAML/CSV architecture model files
/data/                    — zone_catalog.csv, dci_transport.csv
/stencils/drawio/         — Draw.io library XML
/stencils/visio/          — Visio stencil sources
```

---

## 15. Deliverables roadmap

### Scope 1 — Priority

| ID | Deliverable | Primary layer | Status |
|---|---|---|---|
| D0 | Enterprise Architecture Overview | L0 | Refactor |
| D1-CAMPUS | Campus Architecture Overview | L2 | Refactor |
| D1-DC | Data Center Architecture Overview | L2 | Refactor |
| D1-CLOUD | Company Cloud Architecture Overview | L2 | Update |
| D1-EXT | External Networks | L3 | Refactor |
| D1-REMOTE | Remote Workers — Prisma VPN + Proxy + VDI | L3 / L4 | New |
| D1-OOB | MGMT / OOB — Tier I + II | L2 / L4 | New |
| D1-WIFI | Campus WiFi — security and segmentation | L2 / L4 / L6 | New |

### Scope 2

| ID | Deliverable | Primary layer | Status |
|---|---|---|---|
| D2-MON | Monitoring, Alerting and Ticketing | L7 | New |
| D2-FT | Managed File Transfer | L5 | New |
| D2-API | API Gateway | L5 | New |

### Scope 3

| ID | Deliverable | Primary layer | Status |
|---|---|---|---|
| D3-* | Business Flows — per major application | L5 | Planned |

### Alignment to PPTX deliverable structure (merged deck)

The 39-slide merged deck (`NetRefArch_Baseline_V2_MERGED.pptx`) is structured:

| Slides | Content |
|---|---|
| 1–4 | Front matter (title, revision history, purpose, scope/index) |
| 5–29 | Network Principles — 19 principles in 3 categories (A: Design, B: Security, C: Governance) |
| 30–33 | Definitions (architecture model, areas, zones, zone catalog) |
| 34 | Annex divider |
| 35–39 | Annex — Document Design Principles (governance, connector standard, layer model, tooling, roadmap) |

---

## 16. Decisions log

### Baseline V1.0 decisions (approved)

| Decision | Status |
|---|---|
| Use Area / Zone / Actor / Environment / Exposure as separate orthogonal concepts | Approved |
| Approved Areas: Internet, External Networks, Campus, Data Center, Company Cloud | Approved |
| Remote Workers are Actors on the Internet, not an Area | Approved |
| Customers/Clients are Actors on the Internet | Approved |
| Campus Workers and Guests are Actors in Campus | Approved |
| MGMT/OOB is a Zone, not an Area | Approved |
| Lab is a Zone (NONPROD) due to dedicated isolation controls | Approved |
| Zone catalog is extensible | Approved |
| Zones isolated by firewalls, VRFs, ACI EPGs, and routing controls | Approved |
| D0 shows DC1 and DC2 as separate physical entities | Approved |
| D1+ may abstract mirrored DCs as one DC where duplication adds no value | Approved |
| Area colors identify domains | Approved |
| Zone colors indicate exposure posture (green → yellow → orange) | Approved |
| Draw.io is the primary diagram tool | Approved |
| Visio reserved for special high-detail cases | Approved |
| Mermaid is an intermediate representation, not the final rendering engine | Approved |
| Diagram + narrative = deliverable (diagram alone is incomplete) | Approved |
| Color is a soft semantic cue, not an alarm system | Approved |
| Use "Exposure Level" vocabulary, not Trust Level | Approved |

### Baseline V2.0-draft decisions (proposed)

| Decision | Status |
|---|---|
| Connector/line-style standard established (7 types: data, mgmt, OOB, encrypted, partner, replication, temporary) | Proposed |
| Slide frame standard established (section tag / title / footer / no accent lines) | Proposed |
| PowerPoint confirmed as the publishing format for architecture presentations | Proposed |
| Slides built with neutral styling (Calibri / white) for corporate Slide Master portability | Proposed |
| Annex structure adopted: Document Design Principles separated from main body | Proposed |
| PPTX slide size standardised at 13.33" × 7.5" (LAYOUT_WIDE) | Proposed |
| Foundation deck V2 (13 slides) merged with Principles deck (25 slides) = 39-slide combined deliverable | Proposed |
| Consolidation of network_reference_architecture and network_reference_diagrams repositories | Proposed |
| Zone catalog CSV (zone_catalog.csv) established as machine-readable source for zone definitions | Proposed |
| DCI transport CSV (dci_transport.csv) established as machine-readable source for inter-DC connectivity | Proposed |

---

## 17. Changelog

### V2.0-draft — 2025-06-02

**Repository consolidation:**
- Merged `network_reference_architecture` and `network_reference_diagrams` into single consolidated baseline.
- Zone catalog CSV, DCI transport CSV, and object catalog absorbed from diagrams repo.
- Color-coding rationale, color model, and connectivity baseline reconciled and merged.
- Architecture baseline documents from both repos merged into this document.

**Standards additions:**
- Connector / line-style standard defined (Section 10): 7 connector types with color, pattern, weight, and use.
- Flow label format standardised: `Protocol / Port / Purpose` with encrypted and temporary variants.
- Slide frame standard defined: section tag + title + footer + slide number, no decorative accent lines.
- Draw.io layer convention defined (ZONES / DEVICES / CONNECTIONS-data / -mgmt / -oob / LABELS).

**PPTX deliverable:**
- Foundation deck V2 produced (13 slides): title, revision history, purpose, scope, network principles reference, architecture model, area definitions, zone exposure model, zone catalog, connector standard, layer model, tooling, roadmap.
- Merged deck produced (39 slides): foundation front matter + 25 network principles slides + definitions + Annex.
- Company theme (Calibri Light / Calibri, Office colors) applied from `.thmx` file.
- Annex structure adopted: slides 35–39 carry Document Design Principles separated from main body.
- PPTX slide size standardised at 13.33" × 7.5".

**Zone catalog enrichment:**
- Extended zone instances added from zone_catalog.csv (DC, Campus, Cloud named zones).
- MAGICS environment zones (Dev, Qual, Acc, Prod) documented.
- External partner zones (BNPPF, Bloomberg, Portima) documented.
- AVD / Remote workforce zones documented.

### V1.0 — prior session

- Established Area / Zone / Actor model.
- Confirmed Draw.io as primary diagramming tool.
- Added L0–L7 layer model with optional overlays.
- Added D0 context diagram rules.
- Added Scope 1/2/3 deliverable roadmap.
- Added documentation companion checklist.
- Merged relevant content from prior `network_reference_diagrams` repository.
