# AAD — Azure Cosmos DB for NoSQL

| Field | Value |
|---|---|
| Version | 1.0-draft |
| Status | Draft — pending ITAB validation |
| Date | 2026-06-08 |
| Owner | IT Architecture |
| Author | Reda Moukhlissi |
| Review frequency | Yearly |
| Source | `docs/input/AAD - CosmosDB for NoSQL.pdf` |

---

## Table of contents

1. [Purpose](#1-purpose)
2. [Approved use cases](#2-approved-use-cases)
3. [Mandatory fit criteria](#3-mandatory-fit-criteria)
4. [Anti-patterns and disqualifiers](#4-anti-patterns-and-disqualifiers)
5. [Technical architecture](#5-technical-architecture)
6. [Security baseline](#6-security-baseline)
7. [Performance, partitioning and scalability](#7-performance-partitioning-and-scalability)
8. [Integration patterns](#8-integration-patterns)
9. [Operational model](#9-operational-model)
10. [Sovereign exit plan](#10-sovereign-exit-plan)
11. [Governance and lifecycle](#11-governance-and-lifecycle)

---

## 1. Purpose

This AAD defines how Azure Cosmos DB for NoSQL must be used within AG. It sets the baseline architecture, security rules, and operational expectations that project teams must follow when deploying and running Cosmos DB in Azure.

Cosmos DB is **not** the default replacement for SQL Server, Azure SQL Database, Databricks, or the DWH. Its use must be justified case by case based on access pattern, expected scale, cost, and data portability.

**Scope:** NoSQL API only. MongoDB, Cassandra, Gremlin, Table, and PostgreSQL-compatible APIs are out of scope.

---

## 2. Approved use cases

A workload must match at least one of the following:

| Use case | Suitable when |
|---|---|
| High-scale key-based lookup | Very high-throughput point reads/writes addressable by a well-chosen partition key (e.g. policy lookup by policy number) |
| Operational read model / denormalized projection | A denormalized, read-optimized copy of data from the system of record (e.g. 360° customer view) |
| Digital journey or workflow state | Bounded application state retrieved and updated independently via a known identifier |
| User preferences | Application-owned preference data stored as a bounded document, accessed primarily by user identifier |
| Globally distributed operational data | Active-active writes across multiple Azure regions with an appropriate consistency model |
| Heterogeneous catalogue serving | Large, read-heavy catalogue with varied entity types and limited relationships |

---

## 3. Mandatory fit criteria

All of the following must be satisfied. If any is not met, Cosmos DB is likely not the right choice.

| Criteria | Description |
|---|---|
| Access is predominantly key-based | Dominant access path is a point read/write by partition key + item id; no cross-partition queries or cross-entity joins |
| No multi-partition transactions required | Workload does not require atomic transactions spanning more than one logical partition |
| A sound partition key exists | High-cardinality key that distributes throughput evenly and avoids hot partitions over the workload lifetime |
| Analytics served separately | Reporting and aggregation are not served by queries against the operational container |

> The justification must be based on **scale, distribution, latency SLA, change feed, or schema at volume** — not merely that data is JSON or schema-flexible. Azure SQL Database supports JSON natively.

---

## 4. Anti-patterns and disqualifiers

Use a relational database even if Cosmos DB could technically work when:

- **Small or steady data volume** — Cosmos DB is built for scale; without it, you pay for unused capacity.
- **Highly connected data** — Core data with many relationships that need enforcement and frequent multi-entity atomic updates.
- **JSON/schema-flexibility is the only differentiator** — Not a valid reason to choose Cosmos DB.
- **No stable partition boundary** — The workload cannot be cleanly partitioned around a stable business key.

---

## 5. Technical architecture

### Resource hierarchy

Every Cosmos DB deployment follows: **Account → Database → Container → Item**.

### Deployment principles

| Principle | Rationale |
|---|---|
| One Cosmos DB account per application per environment | Clear ownership, isolation, cost allocation, security boundary, lifecycle management |
| Deployed in the owning application landing zone | Keeps the database aligned with the application lifecycle and cost owner |
| Private connectivity is the default and mandatory | Must be accessed through private endpoints integrated with the AG landing zone network model |
| Public network access is forbidden by default | Any exception requires explicit architecture and security approval |
| Direct cross-application access is not the default | Expose data through APIs, events, or data platform flows |

---

## 6. Security baseline

### IAM and access management

| Principle | Rationale |
|---|---|
| Access follows the standard AG IAM process | Requests, approvals, reviews and revocation aligned with IIQ-based RBAC |
| Control-plane and data-plane access must be separated | Managing the Azure resource must not automatically grant access to business data |
| Managed identity is the default for application access | Avoids unmanaged secrets for Azure-hosted workloads |
| Direct human access to production data must be rare and controlled | Must be justified, approved, traceable, and time-bound |
| Key-based access is not the standard | Local keys/connection strings are higher risk; only allowed via approved exceptions |
| Access scoped to minimum required level | Grant at account, database, or container level as appropriate |

### Encryption and key management

| Principle | Rationale |
|---|---|
| Encryption at rest is mandatory for all accounts | Aligns with AG baseline for managed cloud data services |
| CMK must be assessed for Confidential data; mandatory for Highly Confidential or Secret | Provides stronger key ownership control and supports sovereignty requirements |
| Local/key-based authentication disabled by default | Reduces exposure from long-lived secrets |

**Data classification rule:** The classification of a Cosmos DB account is determined by the most sensitive data stored in it. When multiple containers share an account, the highest classification applies to the entire account.

---

## 7. Performance, partitioning and scalability

### Design principles

| Principle | Rationale |
|---|---|
| Performance design based on known access patterns | Cosmos DB only works well if the main access patterns are known upfront |
| Partition key is a mandatory design decision reviewed by the DAE team | Directly impacts scalability, cost, query efficiency, and operational stability |
| Unclear or frequently changing access patterns disqualify Cosmos DB | Such workloads are better suited for relational databases or the data platform |
| RU sizing and throughput mode must be estimated before production approval | Capacity and cost must be understood early to avoid throttling or cost overruns |
| Indexing policy must be reviewed for write-heavy, cost-sensitive, or query-intensive workloads | The default indexing policy may not be optimal for every workload |

### Throughput configuration

| Mode | When to use |
|---|---|
| **Autoscale provisioned throughput** | Default for production workloads with variable or uncertain traffic |
| **Manual provisioned throughput** | Stable workloads with predictable and measured consumption |
| **Serverless** | Dev/test, PoC, prototypes, or low/intermittent workloads — not the default for critical production |
| **Container-level throughput** | Preferred for critical containers requiring specific performance, isolation, or cost control |
| **Database-level shared throughput** | Allowed for small related containers with low/medium traffic and no strict isolation requirement |

---

## 8. Integration patterns

### Application access

- Use the **Azure Cosmos DB SDK** (preferred) or REST API via private endpoints.
- **Managed identity with data-plane RBAC** is the standard authentication method.
- Use **direct mode (TCP)** for production workloads to minimize latency.
- Configure **retry policies** in the application to handle transient failures and throttling.

### Change feed integration

The change feed is the preferred mechanism for event-driven integration.

- Use the **change feed processor library (SDK)** or **Azure Functions Cosmos DB trigger**.
- Change feed consumers must handle **idempotency** — events may be delivered more than once.
- Change feed does **not** capture deletes by default — use soft delete with TTL-based purge if delete propagation is needed.

### Cross-application data access

Direct cross-application access to Cosmos DB is not the default pattern. When data must be shared:

- Expose data through **APIs** owned by the data-owning application.
- Use **event-based integration** (change feed → Event Hub / Service Bus) for asynchronous propagation.
- For analytical consumption, push data to the **data platform** through approved data flows.

### Data platform integration

Cosmos DB must not be used directly for reporting or analytics.

- **Preferred pattern:** Change feed via **Databricks Structured Streaming** (incremental) using the Cosmos DB Spark connector, merging changes into a Delta table on ADLS.
- **Simple/one-time loads:** ADF Copy Activity to export a full container to a Delta table — not the standard for large or frequently updated containers (full export consumes RUs proportional to total data volume).

---

## 9. Operational model

### Monitoring and alerting

Monitoring must follow the AG Azure Monitor reference architecture.

| Principle | Rationale |
|---|---|
| Follow AG Azure Monitor reference architecture | Consistent alert routing and operational governance across Azure workloads |
| Route actionable alerts through the standard alerting chain | Centralized alert orchestration and incident correlation via BigPanda / HEAT |
| Alert payloads must use the standard schema with operational metadata | Improves routing, correlation, and incident handling |
| Forward only actionable alerts to BigPanda / HEAT | Reduces alert fatigue |
| Alert ownership must be explicitly defined per account | Each account must have a clear application/service owner |
| Alerting must be validated before production onboarding | A Cosmos DB workload is not production-ready until alerts, routing, and ownership are validated |

**Alert severity examples:**

| Priority | Examples |
|---|---|
| P1 — Critical | Availability drop, server errors (5xx), throttling, RU saturation |
| P2 — Warning | Rising RU consumption, latency degradation, storage growth, hot partition detected, change feed lag |
| P3 — Informational | Cost anomaly, configuration drift, capacity trends |

### Backup and restore

| Principle | Rationale |
|---|---|
| Built-in Cosmos DB backup is mandatory for all production workloads | Default operational recovery capability |
| Continuous backup / PITR is the preferred production baseline | Supports recovery from accidental deletion, data corruption, or application errors |
| Commvault third-copy backup for CC1/CC2 workloads | Aligns with AG enterprise backup standard for critical applications |
| Commvault is complementary — it does not replace native restore | Both are needed; they serve different recovery scenarios |
| Sovereign exit recovery is addressed separately | Backup alone does not provide a full recovery path outside Azure |

### Disaster recovery

Zone redundancy and cross-region replication must be selected during design based on the workload criticality classification and RPO/RTO requirements. These settings are **immutable** after account creation.

### Non-production data management

Unlike relational databases, Cosmos DB has no server-side procedural language for in-place scrambling. Non-prod data refresh requires an **export → transform (scramble) → import** pipeline.

| Rule | Rationale |
|---|---|
| PII must be masked before use in dev/qual environments with broad access | GDPR and AG data protection policy |
| Acceptance environment may receive unscrambled production data only if access is restricted to production-equivalent controls | Needed for production-issue reproduction and migration validation |
| Each Cosmos DB workload must declare its PII-bearing JSON paths at onboarding | Schema is flexible — PII can live in nested objects or arrays; it cannot be auto-discovered |
| The same masking functions used for relational databases apply — only the addressing changes (JSON paths instead of table.column) | Ensures consistent data protection standards across relational and NoSQL assets |

**Operational considerations for non-prod refresh:**
- Export step consumes RUs on the production account — estimate cost based on data volume.
- Import step should use bulk import mode with temporarily scaled-up non-prod throughput.
- Scrambled and unscrambled JSONL staging files must be deleted after successful import.

---

## 10. Sovereign exit plan

Sovereign exit relies on exporting data in open formats that can be restored outside Cosmos DB — not on native Cosmos DB restore alone.

| Principle | Rationale |
|---|---|
| Every project using Cosmos DB must assess and document its sovereign exit requirement during design | Ensures a conscious, documented decision — not an oversight discovered during a crisis |
| Exit artifacts must use open formats (JSONL, Parquet) | Dependency on Azure-native restore contradicts the goal of sovereignty |
| Exit level (data-only, read-only, or full recovery) must match the workload's criticality classification | Avoids over-engineering exit for all applications |

**Required project design decisions for sovereign exit:**

| Decision | Expected answer |
|---|---|
| Exit requirement | Required / not required, with rationale |
| Recovery level | Data-only, read-only, or full application recovery |
| Recovery target | Target platform or recovery mode |
| Export scope | Account, database, container, or selected data scope |
| Export approach | Baseline and incremental export principle |
| Data format | JSONL, Parquet, or other approved format |
| Delete handling | Soft delete, tombstone, or approved alternative |
| Exit RPO | Based on the last export batch copied outside Azure |
| Restore validation | How the recovery package will be restored and tested |

---

## 11. Governance and lifecycle

### Immutable configuration decisions

The following must be reviewed and confirmed **before first deployment** — they cannot be changed after account creation without recreating the account and migrating all data:

- Zone redundancy
- Partition key
- Account-level API
- Backup mode

### Architecture review gate

No Cosmos DB account may be deployed to production without passing an **architecture review gate** validating:
- Use case fit
- Partition key design
- Security baseline
- Backup strategy
- Sovereign exit assessment

The **partition key must be independently reviewed and approved by the DBA team** — it is a technical validation of cardinality, distribution, and access pattern alignment. A bad partition key is the most common root cause of performance degradation, hot partitions, and cost overruns, and cannot be changed without a full data migration.

### Provisioning RACI (summary)

| Activity | App/Service Owner | Cloud Ops | Database | CyberSecurity | IT Architecture |
|---|---|---|---|---|---|
| Use case & fit validation | R | A | C | I | C |
| Partition key & data model review | C | R | C | I | I |
| Security baseline & network design | I | A | R | C | C |
| Backup, exit strategy & DRP design | C | C | I | A | R |
| IaC template development | C | A | C | C | I |
| Monitoring & alerting setup | R | I | I | C | C |
| Non-prod refresh & scrambling | R/A | I | C | C | I |

### Data schema governance

Each Cosmos DB workload must maintain a **JSON Schema contract** describing expected fields, types, required vs. optional attributes, and nested objects. This schema:
- Is descriptive, not enforced by the database.
- Serves as the source for the **PII field registry** used in the scrambling pipeline.
- Must be maintained in the AG enterprise data catalog.

---

## 12. Known gaps and open points

> This section documents identified gaps in the current version of this AAD. These should be resolved before or during ITAB validation. Items are grouped by priority.

### Document status notes

- This AAD is **Draft — pending ITAB validation**. It is not yet an enforced standard.
- The reviewer comment on the "Lifecycle status" attribute (`Commented [AG1]` in the source PDF) is **unresolved** — the attribute and its allowed values have not yet been approved by ITAB.
- **Contact and Subject Matter Expert fields are blank** in the document control section and must be filled before formal publication.
- A **throughput selection logic diagram** referenced in section 7 of the source PDF did not transfer to text — the decision tree for choosing throughput mode is absent from this document.
- The **RACI table** (section 11) was a visual colour-coded table in the source PDF; the version above is a best-effort text reconstruction and should be verified against the original.

---

### High priority gaps

**1. Consistency levels — no guidance provided.**
Cosmos DB offers five consistency levels (Strong, Bounded Staleness, Session, Consistent Prefix, Eventual), each with direct implications on latency, cost, and data correctness. This is one of the most consequential design decisions when deploying Cosmos DB and the AAD does not address it at all. Guidance needed: which levels are approved per use case, which is the default recommendation, and which are restricted.

**2. Conflict resolution for multi-region writes.**
"Globally distributed operational data" with active-active writes is an approved use case, but the document does not address conflict resolution policies. When two regions write to the same item concurrently, a resolution strategy must be defined — either Last Write Wins (LWW, configurable by timestamp property) or a custom merge procedure. This must be specified for any workload using multi-region writes.

**3. Audit logging and diagnostic settings.**
Despite a strong security baseline (CMK, RBAC, private endpoints), the document contains no requirements for Cosmos DB diagnostic logs. Missing guidance: which log categories must be enabled (DataPlaneRequests, ControlPlaneRequests, QueryRuntimeStatistics, etc.), which Log Analytics workspace they must be sent to, and who is accountable for enabling and maintaining them.

**4. DRP testing requirements — referenced but empty.**
Section 2 lists DRP testing expectations as in scope, and the RACI includes a "Restore & DRP testing" row, but no actual content exists. Missing: testing frequency, test methodology, what constitutes a passing test, and who signs off on the result.

---

### Medium priority gaps

**5. "All Versions and Deletes" change feed mode not mentioned.**
Section 8 states that change feed does not capture deletes by default and recommends soft delete + TTL as a workaround. Azure Cosmos DB now supports an "All Versions and Deletes" feed mode that natively captures deletes (including TTL expirations) with full metadata. The AAD should either incorporate this mode or explicitly state why it is not approved for AG workloads.

**6. Indexing policy guidance is insufficient.**
Section 7 states indexing policy "must be reviewed" for write-heavy or query-intensive workloads but gives no substance. Missing: what the default indexing policy covers, when to exclude paths to reduce write RU cost, when to add composite indexes, and who conducts the review and against what criteria.

**7. Entra ID / RBAC role mapping absent.**
Managed identity is mandated as the default access pattern, but the document does not map Cosmos DB built-in RBAC roles (e.g. Cosmos DB Built-in Data Reader, Cosmos DB Built-in Data Contributor) to access scenarios. Project teams have no guidance on which role to assign to which identity type.

**8. Network setup details missing.**
Private endpoints are mandatory but the document provides no guidance on implementation: private DNS zone configuration, VNet peering requirements, or alignment with the AG hub-spoke network model. Teams deploying their first Cosmos DB account need this to configure the network correctly.

---

### Lower priority gaps

**9. Cost governance.**
Cost is referenced in RU sizing (section 7) and as a P3 alert (section 9) but there is no dedicated cost governance guidance: no mention of Azure Cost Management integration, reserved capacity assessment, showback/chargeback model, or RU right-sizing review cadence beyond the RACI entry.

**10. Decommissioning procedure.**
Decommissioning appears in the RACI but has no associated content. Missing: required data exports before deletion, retention periods for exported data, approval steps for account deletion, and who executes the deletion.

**11. RU estimation methodology.**
Section 7 requires an RU estimate before production approval but provides no guidance on how to produce one — capacity calculator usage, benchmark testing approach, or load simulation expectations.

**12. Out-of-scope APIs — no redirect.**
MongoDB, Cassandra, Gremlin, Table, and PostgreSQL-compatible APIs are declared out of scope with no pointer to an alternative AAD or process. Teams needing those APIs have no direction.
