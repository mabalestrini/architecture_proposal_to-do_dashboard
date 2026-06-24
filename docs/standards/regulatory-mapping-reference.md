# Network Architecture Principles — Regulatory Framework Reference

## Purpose

This document is a reference companion to the 19 Network Architecture Principles. It is not a slide. It is intended for architects, auditors, and compliance personnel who need to understand the substantive link between each principle and the regulatory frameworks it satisfies.

For each principle this document:
- States the principle and its architectural intent
- Identifies the applicable DORA articles and ISO/IEC 27001:2022 Annex A controls
- Provides the key regulatory passage that establishes the link
- Explains why the connection is substantive, not incidental

The source of authority for each principle is engineering best practice and sound design judgement. The regulatory references confirm that these practices are recognised and expected by regulators — they are not the reason the principles exist.

---

## Category A — Design & Operational

---

### 01 · Secure by Design
**DORA: Art. 5, 6, 9 | ISO 27001:2022: 8.27**

Security is integrated throughout the system lifecycle — from design to decommissioning — not bolted on afterwards. Mandatory architecture review by Cybersecurity and the Architecture Board is the organisational mechanism that operationalises this.

**DORA — Article 5(2):**
> "Financial entities shall have in place a sound, comprehensive and well-documented ICT risk management framework as part of their overall risk management system, which enables them to address ICT risk quickly, efficiently and comprehensively and to ensure a high level of digital operational resilience."

**DORA — Article 9(3):**
> "Financial entities shall develop, document and implement a policy on the security of networks and information systems, which shall specify at minimum the type of ICT assets that shall be included in the scope, the security measures that shall be applied and the responsibilities of staff."

**ISO 27001:2022 — Control 8.27 (Secure system architecture and engineering principles):**
> "Principles for engineering secure systems shall be established, documented, maintained and applied to any information system implementation activities."

The link is structural: DORA requires a comprehensive, documented framework and a network security policy; ISO 27001 requires that secure engineering principles are applied at implementation time. Both are satisfied only when security is a design input, not a post-deployment addition. A network designed without documented security principles cannot demonstrate compliance with either framework regardless of what controls are subsequently added.

---

### 02 · Scalable & Modular
**DORA: Art. 7, 9 | ISO 27001:2022: 8.6**

The network handles growth in load, sites and services without re-architecting. Components are modular and independently expandable.

**DORA — Article 7(1):**
> "Financial entities shall use and maintain updated ICT systems, protocols and tools that are: (a) appropriate to the magnitude of operations supporting the conduct of their activities, in accordance with their proportionality principle; (b) reliable; (c) equipped with sufficient capacity to accurately process the data necessary for the performance of activities and the timely provision of ICT services, and to deal with peak orders, message or transaction volumes as needed."

**DORA — Article 9(4)(a):**
> "Financial entities shall design ICT systems in accordance with capacity and performance requirements, so that they can withstand sufficient volumes of ICT-related transactions during peak hours."

**ISO 27001:2022 — Control 8.6 (Capacity management):**
> "The use of resources shall be monitored and adjusted in line with current and expected capacity requirements."

DORA's requirement to design for peak volume directly mandates scalable architecture. ISO 27001's capacity management control requires an active planning cycle — architectural headroom alone is insufficient without monitored thresholds and forecast-driven review. Spine-leaf topology and cloud elasticity are the architectural mechanisms; a documented capacity planning cadence is the governance mechanism that satisfies both frameworks.

---

### 03 · Performant
**DORA: Art. 9 | ISO 27001:2022: 8.6**

Latency, throughput and jitter are engineered to meet business and user-experience requirements.

**DORA — Article 9(4)(a):**
> "Financial entities shall design ICT systems in accordance with capacity and performance requirements, so that they can withstand sufficient volumes of ICT-related transactions during peak hours."

**ISO 27001:2022 — Control 8.6 (Capacity management):**
> "The use of resources shall be monitored and adjusted in line with current and expected capacity requirements."

Both frameworks treat performance as a managed requirement, not an aspiration. DORA makes it explicit: systems must be designed to perform under load. ISO 27001 adds that performance must be continuously monitored and capacity actively managed. QoS and path optimisation are the architectural mechanisms; formally defined performance SLAs per traffic class and service tier — with degradation thresholds that trigger a capacity review — are the governance mechanisms that satisfy both.

---

### 04 · Highly Available & Resilient
**DORA: Art. 11, 12 | ISO 27001:2022: 8.14 · 5.30**

The network survives component, link, site and provider failures and recovers within defined RTO/RPO.

**DORA — Article 11(1):**
> "As part of the ICT risk management framework referred to in Article 6(1), financial entities shall put in place a comprehensive ICT business continuity policy, which may be adopted as a dedicated specific policy, forming an integral part of the overall business continuity policy of the financial entity."

**DORA — Article 11(6):**
> "As part of the ICT business continuity policy, financial entities shall implement response and recovery plans that allow for the recovery of critical or important functions and shall, at least once per year, test those plans."

**DORA — Article 12(1):**
> "For the purpose of ensuring the restoration of ICT systems and data with minimum downtime, limited disruption and loss, as part of their ICT risk management framework, financial entities shall develop and document backup policies and procedures specifying the scope of the data that is subject to the backup and the minimum frequency of the backup, based on the criticality of information or the sensitivity of the data."

**ISO 27001:2022 — Control 8.14 (Redundancy of information processing facilities):**
> "Information processing facilities shall be implemented with sufficient redundancy to meet availability requirements."

**ISO 27001:2022 — Control 5.30 (ICT readiness for business continuity):**
> "ICT readiness shall be planned, implemented, maintained and tested based on business continuity objectives and ICT continuity requirements."

DORA mandates that BCM plans are actively tested at least annually — not merely documented. The semi-annual DC switchover as a live DR exercise is the direct operational implementation of this requirement. ISO 27001's 5.30 further requires that ICT readiness is specifically tested, not assumed from the presence of redundant infrastructure. Together, they require that RTO/RPO targets for the network layer are formally defined, built into the BCM plan, and validated through the switchover exercise.

---

### 05 · Observable
**DORA: Art. 10, 17 | ISO 27001:2022: 5.33**

Network state — availability, performance and security — is continuously measured, correlated and made actionable.

**DORA — Article 10(1):**
> "Financial entities shall have in place mechanisms to promptly detect anomalous activities, including ICT network performance issues and ICT-related incidents, and to identify potential material single points of failure."

**DORA — Article 10(2):**
> "The detection mechanisms referred to in paragraph 1 shall enable multiple layers of control, define alert thresholds and criteria to trigger and initiate ICT-related incident response processes, including automatic alert mechanisms for relevant staff in charge of ICT-related incident response."

**DORA — Article 17(1):**
> "Financial entities shall define, establish and implement an ICT-related incident management process to detect, manage and notify ICT-related incidents."

**ISO 27001:2022 — Control 5.33 (Protection of records):**
> "Records shall be protected from loss, destruction, falsification, unauthorized access and unauthorized release."

DORA requires that detection capability is multi-layered with defined thresholds — this mandates active monitoring infrastructure (NMS, AIOps, NetFlow, SIEM), not passive logging. ISO 27001's 5.33 adds a dimension DORA does not spell out at this level: the logs and records produced by observability tooling must be protected from tampering and deletion, retained for a defined period, and available for audit on demand. A monitoring platform that generates logs that can be overwritten or deleted does not satisfy this control regardless of how comprehensive the monitoring itself is.

---

### 06 · Consistently Pathed
**DORA: Art. 9 | ISO 27001:2022: 8.20**

Equivalent traffic follows equivalent paths. Deviations exist only as documented, justified exceptions.

**DORA — Article 9(2):**
> "Financial entities shall design, procure and implement ICT security policies, procedures, protocols and tools that aim to ensure the resilience, continuity and availability of ICT systems, in particular for those supporting critical or important functions, and to maintain high standards of availability, authenticity, integrity and confidentiality of data."

**ISO 27001:2022 — Control 8.20 (Networks security):**
> "Networks and network devices shall be secured, managed and controlled to protect information in systems and applications."

Unpredictable routing is a security problem, not only an operational one. Traffic that takes unexpected paths may bypass security inspection, creating uncontrolled flows at zone boundaries and undermining the deny-by-default model. ISO 27001's 8.20 requires that the network is actively managed and controlled — consistent, documented paths are a precondition for that control to be effective. Undocumented path deviations constitute a gap in network security management under this control.

---

### 07 · Sustainably Managed & Automated
**DORA: Art. 8, 9 | ISO 27001:2022: 8.9 · 5.37**

Configuration, deployment and lifecycle tasks are automated and version-controlled. Manual change is the exception.

**DORA — Article 9(4)(f):**
> "Financial entities shall implement policies and procedures for ICT change management, including changes to software, hardware, firmware components, systems or security parameters, following a risk-based approach and the applicable ICT change management procedures."

**ISO 27001:2022 — Control 8.9 (Configuration management):**
> "Configurations, including security configurations, of hardware, software, services and networks shall be established, documented, implemented, monitored and reviewed."

**ISO 27001:2022 — Control 5.37 (Documented operating procedures):**
> "Operating procedures for information processing facilities shall be documented and made available to personnel who need them."

Automation serves both frameworks: DORA's change management requirement is satisfied when changes are scripted, version-controlled and repeatable. ISO 27001's configuration management control (8.9) requires that security configurations are documented and reviewed — IaC satisfies this by making configuration the authoritative, versioned record. Control 5.37 requires documented operating procedures for all information processing facilities — automation scripts and runbooks constitute those procedures. Manual, undocumented operations are non-compliant with both frameworks.

---

### 08 · Lifecycle-Managed
**DORA: Art. 8, 9 | ISO 27001:2022: 8.10**

Hardware, software and firmware are tracked from introduction to retirement. End-of-Life / End-of-Support is anticipated and remediated before it occurs.

**DORA — Article 8(1):**
> "As part of the ICT risk management framework, financial entities shall keep an updated register of ICT assets and shall identify and document all processes dependent on ICT third-party service providers, including those located on remote sites, third-party sites or cloud environments."

**DORA — Article 9(4)(e):**
> "Financial entities shall implement policies and procedures to manage ICT assets, covering the full lifecycle of those assets from acquisition to decommissioning."

**ISO 27001:2022 — Control 8.10 (Information deletion):**
> "Information stored in information systems, devices or in any other storage media shall be deleted when no longer required."

DORA covers the active lifecycle explicitly: identification, patching, and the full asset lifecycle. ISO 27001's 8.10 closes the end-of-life gap that DORA addresses less specifically: network devices that are decommissioned must have configurations, credentials and routing tables removed before disposal or reuse. This is not a data hygiene matter — retained device configurations expose network topology, firewall rules, and credentials. An unmanaged decommission is a named control failure under 8.10.

---

## Category B — Security

---

### 09 · Concentration-Aware
**DORA: Art. 28, 29, 30 (RTS on concentration risk) | ISO 27001:2022: 5.23**

No single geographic location, technology vendor, or critical function creates a structural dependency the organisation cannot exit or survive without. Concentration across all three axes is identified, bounded, and governed as a design-time criterion — not discovered as a post-incident finding.

This principle is deliberately distinct from principle 04 (Highly Available & Resilient) and principle 18 (Third-Party Risk-Managed). HA addresses survival of *temporary* failures; concentration-awareness addresses *permanent* structural dependencies. Principle 18 governs how third-party relationships are managed once entered; this principle governs whether the architecture is substitutable in the first place. A well-managed relationship with a non-substitutable vendor satisfies principle 18 but fails principle 20. The separation is required because DORA's RTS mandates formal, evidenced concentration assessments as preconditions for entering ICT dependencies — collapsing these concerns into HA or third-party risk management makes them invisible to the regulatory and audit lens.

**DORA — Article 28(1):**
> "Financial entities shall manage ICT third-party risk as an integral component of ICT risk within their ICT risk management framework... Financial entities shall keep an updated register of all contractual arrangements with ICT third-party service providers."

**DORA — Article 30(1):**
> "Financial entities shall appropriately manage concentration risks when considering taking up new contractual arrangements on the use of ICT services... they shall assess whether the envisaged entering into of a contractual arrangement on the use of ICT services would create: (a) a contractual arrangement with an ICT third-party service provider that is not easily substitutable; or (b) multiple contractual arrangements with the same ICT third-party service provider or with ICT third-party service providers belonging to the same group."

**DORA — Article 30(2):**
> "Financial entities shall assess the risks associated with the concentration of ICT third-party service providers in relation to specific providers, to geographical locations, in view of heightened geopolitical risks, and to functionalities."

**DORA RTS on ICT Concentration Risk — Substitutability and Geographic Assessment:**
> Risk assessments shall include considerations for risks associated with data availability, the physical locations of data processing and storage, and the geographic location of the ICT service provider itself. The criteria for designation of Critical Third-Party Providers include a substitutability rating among the quantitative criteria.

**ISO 27001:2022 — Control 5.23 (Information security for use of cloud services):**
> "Processes for acquisition, use, management and exit from cloud services, in relation to the organization's information security requirements, shall be established."

The three axes — geographic, provider, functional — each require distinct treatment:

**Geographic concentration:** DORA Article 30(2) explicitly names "geographical locations, in view of heightened geopolitical risks" as a named axis of concentration risk. This is not the same as HA: two datacenters 50 km apart satisfy HA but would not satisfy geographic concentration requirements if both were in the same country subject to the same regulatory intervention, or if all cloud workloads were in a single Azure region. The principle requires that critical functions can be operated from geographically independent locations, and that data residency requirements are met by architecture — not by exception handling.

**Provider/vendor concentration:** DORA's RTS introduces a formal "substitutability rating" as a quantitative criterion for assessing critical third-party providers. The ECB has documented that over 70% of European banks depend on just two cloud providers — a systemic concentration that supervisors now treat as a governance failure, not merely an operational risk. The principle requires that substitutability is assessed before a vendor dependency is accepted, and that standards-based interfaces are preferred over proprietary ones to preserve the ability to substitute without architectural redesign.

**Functional concentration:** Where a single technology type or protocol creates a dependency with no credible alternative path — whether through proprietary lock-in, lack of market alternatives, or accumulated technical debt — the organisation is exposed to a functional concentration risk that neither vendor diversity nor geographic distribution can address. The principle requires that such dependencies are documented as governed exceptions, not silent architectural defaults.

**ISO 27001:2022 — Control 5.23** links this to cloud services specifically: the requirement to establish processes for "exit from cloud services" is only satisfiable if the architecture was designed with substitutability in mind. An organisation that has never assessed exit feasibility cannot demonstrate compliance with this control, regardless of how well the cloud relationship is managed under principle 18.

The combination of DORA Article 30's explicit geographic and substitutability requirements, the RTS concentration risk assessments, and ISO 27001's cloud exit process obligation together establish that concentration-awareness is a named regulatory expectation — not an architectural preference — for financial entities.

---

*DORA: Regulation (EU) 2022/2554 of the European Parliament and of the Council of 14 December 2022 on digital operational resilience for the financial sector. Passages reproduced for reference purposes; the authoritative text is the Official Journal of the European Union.*

*ISO/IEC 27001:2022: Information security, cybersecurity and privacy protection — Information security management systems. Annex A control text reproduced for reference purposes; the authoritative text is the published standard (ISO/IEC 27001:2022).*

### 10 · Least Privilege
**DORA: Art. 9, 20 | ISO 27001:2022: 8.2 · 8.3**

Users, devices and workloads receive the minimum network access required for their function — no more.

**DORA — Article 9(4)(b):**
> "Financial entities shall implement access control policies and related policies, address network access rights and management of privileged access rights, based on need-to-know, need-to-use and least-privilege principles, and the segregation of duties."

**ISO 27001:2022 — Control 8.2 (Privileged access rights):**
> "The allocation and use of privileged access rights shall be restricted and managed."

**ISO 27001:2022 — Control 8.3 (Information access restriction):**
> "Access to information and other associated assets shall be restricted in accordance with the established topic-specific policy on access control."

DORA names least-privilege as a required principle for access control — this is not a recommended practice but a regulatory obligation. ISO 27001 splits this into two distinct and complementary obligations: privileged access (8.2) requires a managed lifecycle for administrative accounts — not just initial assignment but periodic review and revocation of stale or excessive permissions; information access restriction (8.3) requires that zone-based firewall policy is derived from a documented access control policy, not configured ad hoc. Together they cover both management-plane access (who can administer the network) and data-plane access (what traffic can cross zone boundaries).

---

### 11 · Zero Trust-Aligned
**DORA: Art. 9 | ISO 27001:2022: 8.3 · 8.20**

No entity is implicitly trusted by location. Every access request is authenticated, authorised and continuously evaluated.

**DORA — Article 9(4)(b):**
> "Financial entities shall implement access control policies and related policies, address network access rights and management of privileged access rights, based on need-to-know, need-to-use and least-privilege principles, and the segregation of duties."

**ISO 27001:2022 — Control 8.3 (Information access restriction):**
> "Access to information and other associated assets shall be restricted in accordance with the established topic-specific policy on access control."

**ISO 27001:2022 — Control 8.20 (Networks security):**
> "Networks and network devices shall be secured, managed and controlled to protect information in systems and applications."

DORA's need-to-know and need-to-use requirements are inherently incompatible with implicit location-based trust: if access is granted because a device is physically on-premises, the need-to-use criterion cannot be properly evaluated. ISO 27001's 8.20 requires that networks are actively managed and controlled — a network that grants implicit trust by location is not under managed control. Cisco ISE as the universal policy enforcement point, applying authentication regardless of whether a device is physically on-site or remote, is the concrete implementation that satisfies both frameworks.

---

### 12 · Effectively Segmented
**DORA: Art. 9 | ISO 27001:2022: 8.22**

Macro-segmentation by default; micro-segmentation applied selectively to critical workloads. Complexity is justified.

**DORA — Article 9(4)(c):**
> "Financial entities shall implement policies and procedures to... protect the network against cyber threats, including policies on the monitoring of network traffic and on data security and on the segregation of network to limit the damage resulting from an incident."

**ISO 27001:2022 — Control 8.22 (Segregation of networks):**
> "Groups of information services, users and information systems shall be segregated in networks."

DORA explicitly names network segregation as a mechanism for limiting incident damage — this is a named risk mitigation requirement, not a general best practice. The intent is containment: a breach in one zone must not propagate freely to others. ISO 27001's 8.22 operationalises this: segregation must be applied to groups of services, users and systems. The Areas → Zones → Segments hierarchy with firewall enforcement at every boundary directly satisfies both. The documented rationale for selective rather than universal micro-segmentation — controlling management overhead to prevent complexity from itself becoming a risk — is also a compliance-relevant design decision that should be recorded.

---

### 13 · Encrypted in Transit
**DORA: Art. 9 | ISO 27001:2022: 8.24**

All data in transit is encrypted by default. Unencrypted flows are exceptions — isolated, documented and time-bound.

**DORA — Article 9(4)(d):**
> "Financial entities shall implement policies and procedures to maintain the integrity of software and data, including cryptographic techniques for the protection of data in transit and at rest."

**ISO 27001:2022 — Control 8.24 (Use of cryptography):**
> "Rules for the effective use of cryptography, including cryptographic key management, shall be defined and implemented."

DORA mandates cryptographic protection for data in transit as a named policy obligation — "implement policies and procedures" means the policy must exist as a document, not merely as a practice. ISO 27001's 8.24 goes further: it requires defined rules covering approved algorithms, minimum protocol versions, key lengths, and certificate lifecycle management. The presence of legacy unencrypted flows does not automatically constitute a breach of either framework, provided they are documented as a named, time-bound risk with a defined sunset date and isolated through segmentation. An undocumented unencrypted flow is a breach of both.

---

### 14 · Controlled Trust-Boundary Flows
**DORA: Art. 9, 10 | ISO 27001:2022: 8.12**

Flows from lower-trust to higher-trust zones are deny-by-default and strictly allow-listed. Reverse flows are monitored.

**DORA — Article 9(4)(c):**
> "Financial entities shall implement policies and procedures to... protect the network against cyber threats, including policies on the monitoring of network traffic and on data security and on the segregation of network to limit the damage resulting from an incident."

**DORA — Article 10(1):**
> "Financial entities shall have in place mechanisms to promptly detect anomalous activities, including ICT network performance issues and ICT-related incidents, and to identify potential material single points of failure."

**ISO 27001:2022 — Control 8.12 (Data leakage prevention):**
> "Data leakage prevention measures shall be applied to systems, networks and any other devices that process, store or transmit sensitive information."

DORA's monitoring requirement and detection mandate together require that boundary flows are not only controlled but actively watched — anomalous outbound traffic is as significant as anomalous inbound traffic. ISO 27001's DLP control (8.12) adds a named requirement: outbound monitoring at egress points must be policy-driven and specific — general network monitoring does not satisfy 8.12 unless it is implemented as a defined DLP control with documented policy applied at proxy and internet firewall egress points.

---

### 15 · Identity-Centric
**DORA: Art. 9 | ISO 27001:2022: 8.5**

Identity — user, device, workload — is the primary policy anchor, not IP address or network location.

**DORA — Article 9(4)(b):**
> "Financial entities shall implement access control policies and related policies, address network access rights and management of privileged access rights, based on need-to-know, need-to-use and least-privilege principles, and the segregation of duties."

**ISO 27001:2022 — Control 8.5 (Secure authentication):**
> "Secure authentication technologies and procedures shall be implemented based on information access restrictions and the topic-specific policy on access control."

DORA's access control requirement is identity-native: "need-to-know" and "need-to-use" are identity concepts — they cannot be evaluated if policy is bound to IP addresses that change, are shared, or can be spoofed. ISO 27001's 8.5 requires that authentication assurance levels are defined per access context. Practically, this means MFA is mandatory for privileged access and remote network device management — it is a control requirement under 8.5, not an optional enhancement. The integration of Entra ID with ISE ties verified identity to network policy across all access contexts, satisfying DORA's need-to-use requirement at the access enforcement layer.

---

### 16 · Threat-Detected & Response-Ready
**DORA: Art. 10, 17, 18 | ISO 27001:2022: 5.7**

Threats are detected through layered telemetry, correlated in a SIEM, and routed into a defined response process.

**DORA — Article 10(1):**
> "Financial entities shall have in place mechanisms to promptly detect anomalous activities, including ICT network performance issues and ICT-related incidents, and to identify potential material single points of failure."

**DORA — Article 10(2):**
> "The detection mechanisms referred to in paragraph 1 shall enable multiple layers of control, define alert thresholds and criteria to trigger and initiate ICT-related incident response processes, including automatic alert mechanisms for relevant staff in charge of ICT-related incident response."

**DORA — Article 17(1):**
> "Financial entities shall define, establish and implement an ICT-related incident management process to detect, manage and notify ICT-related incidents."

**DORA — Article 18(1):**
> "Financial entities shall classify ICT-related incidents and shall determine their impact based on the following criteria: the number of clients or financial counterparts affected; the duration of the ICT-related incident; the geographical spread with regard to the areas affected by the ICT-related incident, particularly if it affects two or more Member States."

**ISO 27001:2022 — Control 5.7 (Threat intelligence):**
> "Information relating to information security threats shall be collected and analysed to produce threat intelligence."

DORA's three articles constitute a complete detection-and-response chain: detect anomalies across multiple layers (Art. 10), manage incidents through a defined process (Art. 17), and classify by impact for regulatory reporting (Art. 18). ISO 27001's threat intelligence control (5.7) adds a proactive dimension that DORA implies but does not spell out at this level: the organisation must consume and act on external threat intelligence. Reactive signature matching alone does not satisfy 5.7 — threat intelligence requires collection, analysis and operational use of feeds, sector ISACs, and vulnerability advisories to enable indicator-based detection before attacks are reflected in local signatures.

---

## Category C — Governance & Compliance

---

### 17 · Governed & Compliant
**DORA: Art. 5, 6, 16 | ISO 27001:2022: 5.5**

Architecture, operations and change comply with applicable regulations. Compliance is evidenced, not asserted.

**DORA — Article 5(2):**
> "The management body of the financial entity shall define, approve, oversee and be responsible for the implementation of all arrangements related to the ICT risk management framework referred to in Article 6(1)."

**DORA — Article 6(5):**
> "Financial entities shall have in place a digital operational resilience strategy setting out how the framework shall be implemented. To that end, the strategy shall include methods to promptly detect anomalous activities and criteria to initiate ICT-related incident response and recovery processes, including at least annual testing of the ICT business continuity plans."

**ISO 27001:2022 — Control 5.5 (Contact with authorities):**
> "The organization shall establish and maintain contact with relevant authorities."

DORA places explicit accountability for the ICT risk management framework at management body level — governance is not a technical function delegated to IT, it is a board-level obligation. The architecture review and approval process by Cybersecurity and the Architecture Board is a direct implementation of this. ISO 27001's 5.5 adds a specific obligation that is operationally easy to overlook: a defined notification path to relevant authorities for network-originated incidents. This means a designated contact, applicable regulatory authority, reporting threshold and response timeline must be documented and tested in advance — not improvised during an incident.

---

### 18 · Change-Controlled
**DORA: Art. 8, 9, 16 | ISO 27001:2022: 5.3 · 8.29**

Every change is reviewed, approved, traceable and reversible. Emergency change has a defined fast path with retrospective review.

**DORA — Article 9(4)(f):**
> "Financial entities shall implement policies and procedures for ICT change management, including changes to software, hardware, firmware components, systems or security parameters, following a risk-based approach and the applicable ICT change management procedures."

**DORA — Article 9(4)(g):**
> "Financial entities shall implement policies and procedures for patch and update management."

**ISO 27001:2022 — Control 5.3 (Segregation of duties):**
> "Conflicting duties and conflicting areas of responsibility shall be segregated."

**ISO 27001:2022 — Control 8.29 (Security testing in development and acceptance):**
> "Security testing practices shall be defined and implemented in the development and acceptance lifecycle."

DORA's change management requirement is risk-based and applies to all ICT components including security parameters. ISO 27001 adds two controls that together prevent the most common change-related failures. Control 5.3 means no single individual may author, approve and implement a network change — this is a structural control, not a procedural preference. Control 8.29 means significant network changes — new firewall rules, segmentation modifications, new connectivity — must be validated before production deployment. Together these controls make the change process auditable, safe and resistant to both error and insider manipulation.

---

### 19 · Third-Party Risk-Managed
**DORA: Art. 28, 29, 30 | ISO 27001:2022: 5.19 · 5.22**

ICT third-party dependencies are inventoried, assessed, contractually managed and monitored. Concentration risk is actively managed.

**DORA — Article 28(1):**
> "Financial entities shall manage ICT third-party risk as an integral component of ICT risk within their ICT risk management framework... Financial entities shall keep an updated register of all contractual arrangements with ICT third-party service providers."

**DORA — Article 29(1):**
> "Financial entities shall ensure that contracts on the use of ICT services concluded with ICT third-party service providers include at least the following: a clear and complete description of all functions and ICT services to be provided by the ICT third-party service provider, indicating whether sub-outsourcing of an ICT service supporting a critical or important function is permitted..."

**DORA — Article 30(1):**
> "Financial entities shall appropriately manage concentration risks when considering taking up new contractual arrangements on the use of ICT services... they shall assess whether the envisaged entering into of a contractual arrangement on the use of ICT services would create... a dependency on an ICT third-party service provider that is not easily substitutable."

**ISO 27001:2022 — Control 5.19 (Information security in supplier relationships):**
> "Processes and procedures shall be defined and implemented to manage the information security risks associated with the use of supplier's products or services."

**ISO 27001:2022 — Control 5.22 (Monitoring, review and change management of supplier services):**
> "The organization shall regularly monitor, review, evaluate and manage change in supplier information security practices and service delivery."

DORA's Article 30 on concentration risk is the most operationally significant provision for network architecture: it explicitly requires assessment of whether a third party is "not easily substitutable." This is the regulatory basis for the dual-provider WAN strategy, the requirement for exit strategies on critical services, and the periodic concentration risk review across carrier, cloud region and technology vendor dimensions. ISO 27001's 5.22 adds an ongoing monitoring obligation: supplier security practices and service delivery must be reviewed regularly — third-party relationships are not set-and-forget compliance items.

---

### 20 · Documented & Standardized
**DORA: Art. 8, 9 | ISO 27001:2022: 5.37 · 8.9**

Naming, addressing, configuration and documentation follow defined standards held in a single source of truth.

**DORA — Article 8(1):**
> "As part of the ICT risk management framework, financial entities shall keep an updated register of ICT assets and shall identify and document all processes dependent on ICT third-party service providers, including those located on remote sites, third-party sites or cloud environments, in accordance with the classification scheme referred to in Article 8(1)."

**DORA — Article 9(4)(f):**
> "Financial entities shall implement policies and procedures for ICT change management, including changes to software, hardware, firmware components, systems or security parameters, following a risk-based approach and the applicable ICT change management procedures."

**ISO 27001:2022 — Control 5.37 (Documented operating procedures):**
> "Operating procedures for information processing facilities shall be documented and made available to personnel who need them."

**ISO 27001:2022 — Control 8.9 (Configuration management):**
> "Configurations, including security configurations, of hardware, software, services and networks shall be established, documented, implemented, monitored and reviewed."

DORA's asset register requirement is only satisfiable if naming and addressing are standardised — an environment without consistent naming conventions cannot produce a reliable, complete, auditable inventory. ISO 27001's 8.9 makes configuration documentation a named security control obligation, not operational hygiene. Control 5.37 requires that operating procedures are documented and available to personnel who need them — this means naming conventions, IP address management procedures and configuration standards must exist as accessible documents, not tribal knowledge. Together, the reference architecture, IPAM, CMDB and naming standards are compliance deliverables, not optional documentation effort.

---

---
