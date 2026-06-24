# Network Architecture Principles

**Status: Standing reference — read-only for all projects using this template.**

Any solution designed using this template must comply with these principles. They are not deliverables of the project — they are inherited constraints. If a proposed design conflicts with a principle, the conflict must be documented and a risk acceptance recorded.

---

## Implementation verification markers

- ✓ Confirmed — directly evidenced
- ! Partial — concept confirmed, detail needs verification
- ? To be checked — requires confirmation against current state

---

## Categories

- **A — Design & Operational** (01–08)
- **B — Security** (09–15)
- **C — Governance & Compliance** (16–19)

---

## A — Design & Operational

### 01 · Secure by Design
Security is integrated throughout the system lifecycle — from design to decommissioning — not bolted on afterwards.

**Rationale:** Vulnerabilities introduced at design time are the most expensive to fix. Addressing security at the point of design prevents structural weaknesses from being embedded in the architecture.

**Implementation:**
- ✓ Mandatory architecture review (Cybersecurity + Architecture board) for all new/modified designs
- ? Threat modelling on critical changes; risk-acceptance recorded
- ✓ Physical protection of active network components; controlled access to network rooms

DORA: Art. 5, 6, 9

---

### 02 · Scalable & Modular
The network handles growth in load, sites and services without re-architecting. Components are modular and independently expandable.

**Rationale:** A network that cannot scale forces disruptive re-architecture under pressure. Modular design bounds the scope of change and allows the network to grow with the business without introducing fragility.

**Implementation:**
- ✓ Spine-leaf topology in datacenters for horizontal scale
- ! Cisco ACI as policy/SDN layer decoupling intent from hardware
- ? Capacity planning reviewed at least annually

DORA: Art. 7, 9

---

### 03 · Performant
Latency, throughput and jitter are engineered to meet business and user-experience requirements.

**Rationale:** Poor network performance degrades productivity and disrupts latency-sensitive services. Performance must be designed in — not treated as a tuning problem after the fact.

**Implementation:**
- ✓ Cisco Catalyst Center centrally manages QoS policy and deployment
- ✓ High-bandwidth inter-DC links reduce the need for QoS inside the DC fabric
- ✓ Path optimisation and split-tunnelling for latency-sensitive traffic

DORA: Art. 9

---

### 04 · Highly Available & Resilient
The network survives component, link, site and provider failures and recovers within defined RTO/RPO.

**Rationale:** Any single point of failure in a network that underpins critical business operations is unacceptable. Resilience must be engineered at every layer — device, link, site and provider.

**Implementation:**
- ✓ Two mirrored datacenters (DC1/DC2), Active/Passive, with semi-annual DC switchover as live DR exercise
- ! Dual-provider redundancy on HQ and main sites; single-provider sites carry documented risk acceptance
- ✓ Spine-leaf fabric with redundant paths; redundant firewall clusters

DORA: Art. 11, 12

---

### 05 · Observable
Network state — availability, performance and security — is continuously measured, correlated and made actionable.

**Rationale:** Without continuous visibility, anomalies go undetected, capacity problems surface too late, and incident response is reactive rather than proactive.

**Implementation:**
- ✓ Availability monitoring: dedicated NMS platform; alert de-duplication and correlation via AIOps
- ✓ Performance: NetFlow analytics for capacity and path behaviour
- ! Security telemetry: firewall, proxy and IDPS logs consolidated; NDR and SIEM integration to be confirmed

DORA: Art. 10, 17 | ISO: 5.33

---

### 06 · Consistently Pathed
Equivalent traffic follows equivalent paths. Deviations exist only as documented, justified exceptions.

**Rationale:** Unpredictable paths make the network harder to secure and harder to troubleshoot. Consistent paths are a prerequisite for reliable performance and effective security inspection.

**Implementation:**
- ✓ Standardised end-user access path into a single logical internal path
- ✓ BGP for deterministic routing between on-prem and cloud
- ✓ Documented exceptions with security rationale

DORA: Art. 9

---

### 07 · Sustainably Managed & Automated
Configuration, deployment and lifecycle tasks are automated and version-controlled. Manual change is the exception.

**Rationale:** Manual, undocumented changes are the primary source of configuration drift and untraceable incidents. Automation makes operations consistent and repeatable.

**Implementation:**
- ✓ Infrastructure-as-Code (Terraform) for standard cloud deployments
- ✓ Scripted configuration management for on-prem devices
- ✓ Cross-stack integration: ACI + ISE policy consumed by perimeter firewalls

DORA: Art. 8, 9

---

### 08 · Lifecycle-Managed
Hardware, software and firmware are tracked from introduction to retirement. End-of-Life / End-of-Support is anticipated and remediated.

**Rationale:** Unmanaged asset lifecycles result in unsupported hardware and software remaining in production — creating unpatched vulnerabilities and operational brittleness.

**Implementation:**
- ? Asset inventory with EoL/EoS dates; refresh planned ahead of vendor cutoff
- ? Patch and firmware cadence defined per device class
- ? Decommissioning procedure with secure wipe and configuration removal

DORA: Art. 8, 9 | ISO: 8.10

---

## B — Security

### 09 · Least Privilege
Users, devices and workloads receive the minimum network access required for their function — no more.

**Rationale:** Broad access rights amplify the impact of any compromise. Restricting access limits the blast radius and constrains lateral movement.

**Implementation:**
- ✓ Role-Based Access Control and ACLs on all network devices and management tooling
- ✓ Privilege tied to source segment + requested service via firewall policy
- ✓ Just-in-time / privileged-access workflows for high-impact changes

DORA: Art. 9, 20

---

### 10 · Zero Trust-Aligned
No entity is implicitly trusted by location. Every access request is authenticated, authorised and continuously evaluated.

**Rationale:** Network location is not a reliable indicator of trustworthiness. Requiring explicit authentication for every access request — regardless of origin — removes this structural weakness.

**Implementation:**
- ✓ Cisco ISE authenticates and authorises every access request regardless of location
- ! Device posture and identity context fed into network and firewall policy
- ? Documented ZTA maturity roadmap

DORA: Art. 9

---

### 11 · Effectively Segmented
Macro-segmentation by default; micro-segmentation applied selectively to critical workloads. Complexity is justified.

**Rationale:** A flat network allows any compromised asset to reach any other. Segmentation limits attack propagation and isolates critical assets.

**Implementation:**
- ✓ Network divided into Areas → Zones → Segments with explicit trust levels
- ✓ SDN-based segmentation across LAN, Wi-Fi, VPN
- ! Micro-segmentation reserved for critical assets

DORA: Art. 9

---

### 12 · Encrypted in Transit
All data in transit is encrypted by default. Unencrypted flows are exceptions, isolated and time-bound.

**Rationale:** Encryption ensures that interception does not equate to compromise. Unencrypted flows must be treated as a known risk, explicitly accepted, isolated, and eliminated over time.

**Implementation:**
- ? TLS minimum version and approved cipher policy enforced centrally
- ? PKI with managed certificate lifecycle
- ! Legacy unencrypted flows isolated in remediation zones with sunset dates

DORA: Art. 9

---

### 13 · Controlled Trust-Boundary Flows
Flows from lower-trust to higher-trust zones are deny-by-default and strictly allow-listed. Reverse flows are monitored.

**Rationale:** Every uncontrolled flow across a trust boundary is a potential attack vector. Denying by default concentrates inspection effort where the risk is highest.

**Implementation:**
- ✓ Deny-by-default inbound on every zone boundary; allow-listed by service
- ✓ L7 inspection (IDPS, web proxy, WAF) at high-risk boundaries
- ! Outbound flows monitored for DLP, C2 and policy violation

DORA: Art. 9, 10 | ISO: 8.12

---

### 14 · Identity-Centric
Identity (user, device, workload) is the primary policy anchor — not IP address or network location.

**Rationale:** IP addresses are not identities. In hybrid and cloud environments they are unreliable policy anchors. Binding policy to verified identity produces controls that remain effective regardless of where a principal connects from.

**Implementation:**
- ✓ ISE as the authentication and authorisation policy point on the network
- ? Workload identity in cloud bound to network policy
- ! Identity directory (Entra ID) integrated with network policy and ISE

DORA: Art. 9 | ISO: 8.5

---

### 15 · Threat-Detected & Response-Ready
Threats are detected through layered telemetry, correlated in a SIEM, and routed into a defined response process.

**Rationale:** No security control is perfect. The question is not whether an attack will occur but how quickly it will be detected and contained.

**Implementation:**
- ! Firewall, proxy and IDPS telemetry consolidated; NDR and SIEM integration to be confirmed
- ! Signature-based detection via IDPS; behavioural detection to be confirmed
- ? Defined incident response runbooks, on-call rotation and post-incident review

DORA: Art. 10, 17, 18 | ISO: 5.7

---

## C — Governance & Compliance

### 16 · Governed & Compliant
Architecture, operations and change comply with applicable regulations and internal policy. Compliance is evidenced, not asserted.

**Rationale:** Governance without evidence is an assertion. A network that cannot demonstrate adherence to its own policies and applicable regulations is not under control.

**Implementation:**
- ! Alignment with DORA, NIS2, ISO/IEC 27001, GDPR and applicable sector regulation
- ! Internal and external audit cycle; findings tracked to closure
- ✓ Documentation kept current (reference architecture, modelling, runbooks)

DORA: Art. 5, 6, 16 | ISO: 5.5

---

### 17 · Change-Controlled
Every change is reviewed, approved, traceable and reversible. Emergency change has a defined fast path with retrospective review.

**Rationale:** Uncontrolled change is the leading cause of self-inflicted outages. Control is the mechanism that makes the network trustworthy over time.

**Implementation:**
- ? CAB review for standard, normal and emergency changes
- ? Peer review on IaC pull requests; pipeline gates for policy compliance
- ? Rollback plan mandatory; post-implementation verification recorded

DORA: Art. 8, 9, 16 | ISO: 5.3, 8.29

---

### 18 · Third-Party Risk-Managed
ICT third-party dependencies (carriers, cloud, partners, vendors) are inventoried, assessed and contractually managed. Concentration risk is monitored.

**Rationale:** The network's availability and security posture are only as strong as the weakest external dependency.

**Implementation:**
- ? Register of ICT third parties with criticality classification
- ✓ Dual-provider strategy for critical links
- ? Concentration risk reviewed periodically

DORA: Art. 28, 29, 30

---

### 19 · Documented & Standardized
Naming, addressing, configuration and documentation follow defined standards held in a single source of truth.

**Rationale:** Inconsistent naming and undocumented addressing are compounding problems — each one makes the next change harder and each incident harder to diagnose.

**Implementation:**
- ? Defined naming conventions for VRFs, VLANs, EPGs, ASNs, security zones
- ! Authoritative IPAM and CMDB; configuration backups versioned
- ✓ Reference architecture and modelling maintained and reviewed at least annually

DORA: Art. 8, 9
