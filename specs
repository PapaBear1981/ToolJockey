Tool Jockey — Software Specification (v1.0)

Project type: Tool control & tracking for Aerospace MRO facilities
Audience: Product, Engineering, QA, Compliance, Operations
Doc owner: You (Chris). I’ll try not to drop any wrenches.

Source baseline: Consolidated and expanded from your uploaded “Software Specification for Tool Jockey.” 

Software spec sheet

0) Executive Summary

Tool Jockey is a multi-site tool control and calibration management system for aerospace Maintenance, Repair, and Overhaul (MRO) operations. The system centralizes tool inventory, tracks cradle-to-grave history, enforces calibration and condition rules, supports mobile “Fly Away Kits,” and provides rich reporting. It’s designed to keep tools where they belong, in calibration, and not in the abyss behind Hangar Bay Door #3.

This specification defines scope, requirements (functional & non-functional), architecture, data design, integrations, security and compliance expectations, acceptance criteria, test strategy, deployment plan, and operational runbooks.

1) Project Overview

Name: Tool Jockey

Description: A tool control and tracking system for Aerospace MRO facilities, spanning multiple hangars, fixed warehouses, and mobile “Fly Away Kits.”

Goal / Purpose: Manage and track tooling assets across locations, enforce calibration compliance and condition rules, and provide operational visibility and reporting.

Key Stakeholders:

Tool Room: Daily check-in/out, inventory, quarantine, transfers.

Mechanics / Technicians: Fast checkout, scanning, self-service history lookup.

Managers / Leads: Overrides, quarantine releases, relocations, approvals, reporting.

Quality / Compliance: Audit trails, calibration records/certificates, retention.

IT / Security: Access control, SSO, data protection, infrastructure.

Finance (Informational only): Reports—no costs tracked per out-of-scope.

2) Scope
2.1 In Scope (features & functionality)

Tool check-in/check-out with rules/overrides.

Tool condition management & quarantine.

Full calibration management (send/receive, due dates, certificates).

Warehouse hierarchy and transfers (Warehouse → Zone → Shelf; oversized storage; long-term floor checkouts).

Complete tool history (cradle-to-grave) by serial number.

Tool serialization (manufacturer serial preferred; system-assigned allowed).

Photos of tools.

RBAC with Admin/Manager/User and capability-level grants.

SQL database with offline SQLite fallbacks.

Reporting suite (operational & compliance).

Modern “SAP-style” UI with dark/light themes, profiles, dashboard, alerts.

Barcode/QR scanning, label printing (Zebra/ZPL).

Fly Away Kits (mobile warehouses) with offline usage and later sync.

2.2 Out of Scope

Cost tracking, purchasing, or sales.

HR PII (SSN, address, next of kin, etc.). Only employee ID and optional phone.

Anything financial beyond basic operational reporting.

2.3 Assumptions

All tools must be uniquely identifiable and (ideally) serialized.

Some facilities have intermittent connectivity; offline must be usable.

MRO compliance requires immutable audit logs and calibration traceability.

Multiple time zones and sites; global operation possible.

2.4 Constraints

Zero-tolerance for issuing non-compliant tools unless Manager+ override with reason.

Readable on rugged devices (gloves, glare, low-light).

Photos can be large; storage & CDN required.

Must integrate with enterprise SSO.

3) User Personas & Top Use Cases
3.1 Personas

Tool Room Clerk (primary operator): scan, issue/receive, quarantine; rapid workflows.

Mechanic/Technician (requester): self-service checkout, quick search by number/scan, history glance.

Manager (approver): overrides, quarantine release, transfers, dashboards.

Quality/Compliance Officer: reports, calibrations, certificates, audits.

System Admin (IT): SSO, RBAC, backups, logs.

3.2 Representative Use Cases

Checkout: Clerk scans tool; system auto-checks calibration/condition/location; approves or blocks; records who/when/where; prints label if needed.

Quarantine: Tool returned damaged—clerk marks condition “Damaged,” system auto-quarantines and prevents issuance.

Calibration: Manager creates a “Send for Calibration” work order; on receive, uploads cert PDF, sets next due date.

Transfer: Move tool from GEG/Zone 1/Shelf 3 to DFW/Zone 2/Oversize; updates chain of custody.

Fly Away Kit: Pre-build kit inventory offline; sync on return; reconcile diffs.

4) Detailed Requirements

IDs align and expand on your baseline FR list. 

Software spec sheet

4.1 Functional Requirements

FR-1 — Tool Check-In/Check-Out (Critical)

Rules:

Block checkout if calibration expired, quarantined, or status ≠ serviceable, unless Manager+ override with reason.

Long-term checkout: assign to Floor location or Kit with due date and responsible person.

Support batch scanning (cart of tools) and per-tool notes.

UX:

Single-scan flow (< 5s), keyboardless operation where possible.

Alerts: visual + audible on violations; clear override path.

Data:

Record: who, when, where, status before/after, reason, related work order.

Acceptance Criteria:

Given a tool with expired calibration, when a Clerk attempts checkout, then system blocks with reason and offers Manager override; override requires reason and logs event.

FR-2 — Tool Condition & Management (Critical)

Statuses: Serviceable | Damaged | Out for Repair | Quarantined | Retired.

Quarantine removes visibility from “available” pools.

Acceptance: Updating status to “Damaged” immediately removes tool from issuance lists and creates a required follow-up task.

FR-3 — Calibration Management (Critical)

Create/send calibration orders; track vendor, shipped/received dates, cert number, next due date.

Attach certificate (PDF/image).

Acceptance: On receiving calibration, tool becomes serviceable only if cert uploaded and due date set; history updated.

FR-4 — Warehouse & Location Management (Critical)

Hierarchy: Warehouse (GEG, CIC, DFW, etc.) → Zone → Shelf, plus Oversized and Floor-Assigned logical locations.

Support transfers between warehouses with approvals.

Acceptance: After transfer completion, tool’s current location reflects destination; audit shows from→to, approver, timestamp.

FR-5 — Modern UI/UX with Themes (Critical)

Dark/light, SAP-style density, large hit targets, profile & avatar, dashboard cards for: Due Calibrations, Quarantine Queue, Pending Transfers, Overdue Returns.

Accessibility: WCAG AA minimum; keyboard shortcuts for scanning mode.

Acceptance: Theme change persists per user; dashboard loads < 2s with 1k+ tools.

FR-6 — Tool Photos (Non-Critical)

Upload/view one or more photos per tool type and per serial instance.

Acceptance: JPEG/PNG up to 10MB; resized thumbnails for grid views.

FR-7 — Cradle-to-Grave History (Critical)

Immutable event log: creation, check-in/out, condition changes, calibration, repairs, transfers, retirement, overrides.

Acceptance: For any serial, “History” renders in < 1s for 1k events with filter & export.

FR-8 — Tool Serialization (Critical)

Store manufacturer serial; allow system-assigned serial if missing; uniqueness enforced per tool type.

Acceptance: Duplicate serial per type rejected with explicit error.

FR-9 — RBAC & Login (Critical)

Roles: Admin, Manager, User, plus granular capabilities (e.g., override.issue.blocked, transfer.approve, report.view.compliance).

SSO via OIDC/SAML; SCIM for provisioning (optional).

Acceptance: Capability toggles take effect without redeploy; permission changes logged.

FR-10 — Full Database with Offline SQLite (Critical)

Primary: PostgreSQL.

Offline cache: SQLite on client devices (PWA or desktop), with sync reconciliation on reconnect.

Acceptance: Loss of network does not block local checkout within policy; on reconnect, sync resolves without data loss and flags conflicts for human review.

FR-11 — Reporting Suite (Critical)

Built-in dashboards + ad-hoc queries; export CSV/PDF; scheduled email reports.

Key reports:

Tools due for calibration (next 7/30/60 days)

Quarantined tools by reason & age

Utilization by tool type/location

Transfer throughput & dwell time

Overdue returns and exception logs

Acceptance: Each standard report runs in < 5s on 100k tools.

4.2 Non-Functional Requirements (NFRs)

NFR-1 Performance:

Core checkout flow < 300ms server round-trip (p95) under 200 concurrent users.

Search (tool number/serial) returns first page < 500ms (p95).

NFR-2 Availability: 99.9% monthly SLA for core APIs; offline mode available for issuance & returns.

NFR-3 Security & Privacy:

TLS 1.2+; AES-256 at rest for DB and object storage.

Field-level encryption for phone numbers.

No HR PII beyond employee ID and optional phone (validated).

OWASP ASVS L2 equivalent; quarterly pentest; SAST/DAST in CI.

NFR-4 Compliance & Auditability:

Immutable audit log with tamper-evident hashing.

Calibration records and certificates retained ≥ 7 years.

Role changes and overrides must be fully auditable.

NFR-5 Accessibility: WCAG 2.1 AA.

NFR-6 Maintainability: Clean module boundaries; 85% unit test coverage in domain layer.

NFR-7 Scalability: Horizontally scale API; partition by warehouse/site if needed.

NFR-8 Internationalization (future-ready): Date/time/units locale aware.

5) Architecture & Technology Choices

You asked for help on the tech stack; below is a pragmatic, enterprise-ready baseline plus alternatives.

5.1 High-Level Architecture (Logical)
[ PWA / Web UI ]  <--->  [ API Gateway ]  <--->  [ Services (NestJS) ]
      |                                |            |   + auth service (Keycloak)
      |                                |            |   + tool service
  [Offline SQLite]                 [RBAC/SSO]       |   + warehouse service
      |                                |            |   + calibration service
    [Sync]                          [JWT/OIDC]      |   + reporting service
      v                                v            v
   [PostgreSQL]  <---- events/CDC ---->  [Event Bus/Queue]  ---> [Reporting (Metabase)]
      |
   [Object Storage (S3) for photos & certs]

5.2 Proposed Stack (Recommended)

Frontend:

Next.js 15 (React + TypeScript), TanStack Query, Tailwind CSS, Headless UI, React-Aria for a11y.

PWA features: service workers, background sync, offline cache; local SQLite via WASM (e.g., sql.js/wa-sqlite) or Tauri desktop app where rugged hardware prefers native USB scanners.

Barcode/QR scanning: @zxing/browser (web camera) + optional USB scanner support.

Theming: CSS variables, dark/light, high-contrast mode.

Backend:

Node.js 20 LTS with NestJS (modular, DI, class-validator).

Prisma ORM targeting PostgreSQL (primary) and SQLite (for local desktop workers or service utilities).

REST APIs (OpenAPI/Swagger), webhooks for events, optional GraphQL for reporting readers.

Data & Sync:

Primary DB: PostgreSQL 16 (RDS/Aurora).

Offline DB: SQLite 3 on clients.

Sync approach: ElectricSQL or SymmetricDS (CDC-based) to reconcile Postgres↔SQLite; conflict policy: “server-wins” with human review queue for certain entities (checkouts/transfers).

Identity & RBAC:

Keycloak self-hosted (OIDC/SAML); JWT access tokens, realm & client roles + fine-grained permissions.

Optional enterprise SSO (ADFS/AzureAD/Okta).

Storage:

S3-compatible bucket for images and calibration cert PDFs (immutable versioning).

CloudFront/Cloud CDN for images/thumbnails.

Observability:

OpenTelemetry tracing; Prometheus + Grafana metrics; Loki/ELK logs; Sentry for error monitoring.

Reporting:

Metabase embedded (SSO) with governance; scheduled emails; exports.

Messaging & Jobs:

Redis + BullMQ for background jobs (thumbnailing, PDF generation, scheduled reports).

Optional Kafka if event volumes justify it later.

Printing & Labels:

Zebra ZPL templates; print via networked printers or local agent.

5.3 Alternatives (if org preferences differ)

Backend: FastAPI (Python) or Go + Echo/Fiber.

Auth: Auth0 / Azure AD B2C.

Reporting: Apache Superset.

Desktop: Tauri or Electron (deep offline, serial scanners).

6) Data Model (Core Entities)

High-level schema; actual DDL delivered in implementation. PKs are UUID unless noted.

6.1 Inventory & Structure

warehouse: id, code (GEG/CIC/DFW…), name, timezone, active

zone: id, warehouse_id, name, type (standard|oversize|floor)

shelf: id, zone_id, label

flyaway_kit: id, name, warehouse_id, status (active|in_transit|returned)

6.2 Tools & Types

tool_type: id, tool_number, name, description, requires_serial (bool), calibration_interval_days (nullable), photo_url

tool (physical item): id, tool_type_id, serial_number (unique per type),
status (serviceable|damaged|quarantined|out_for_repair|retired),
current_location (polymorphic: shelf_id | zone_id | flyaway_kit_id | 'floor:<area>), last_calibrated_at, calibration_due_at, notes`

tool_photo: id, tool_id, url, caption

tool_history_event: id, tool_id, type, data (jsonb), actor_user_id, timestamp, hash_prev, hash_this

6.3 Transactions

checkout: id, tool_id, issued_to_user_id, issued_by_user_id, issued_at, due_at,
returned_at, return_condition, notes, override_applied (bool), override_reason

transfer: id, tool_id, from_location, to_location, requested_by, approved_by, status, timestamps…

6.4 Calibration & Repair

calibration_record: id, tool_id, vendor_id, sent_at, received_at, certificate_number, certificate_url, result (pass|fail), next_due_at

repair_record: id, tool_id, vendor_id, sent_at, received_at, work_summary, attachments[]

6.5 People & Access

user: id, employee_id, name, phone (enc), email, status

role: id, name (admin|manager|user)

permission: id, code (e.g., override.issue.blocked)

user_role: user_id, role_id

role_permission: role_id, permission_id

user_permission: direct grants for exceptions

7) API (Selected Endpoints)

All endpoints bearer-token protected (JWT). OpenAPI spec auto-generated.

POST /api/v1/tools/checkout
Body: { toolId, issuedToUserId, longTerm?:bool }
Responses: 200 OK { checkoutId } | 409 { reason:"expired_calibration" }

POST /api/v1/tools/checkin
Body: { checkoutId, condition, notes?, photos?[] }

POST /api/v1/tools/{id}/quarantine
Body: { reason, note }

POST /api/v1/tools/{id}/calibration/send / /receive
Body (receive): { certificateUrl, certificateNumber, nextDueAt }

POST /api/v1/transfers / POST /api/v1/transfers/{id}/approve

GET /api/v1/reports/calibration-due?window=30

GET /api/v1/tools/{id}/history (streams event log)

Webhooks: tool.quarantined, tool.checkout.blocked, calibration.dueSoon, transfer.completed.

8) UI/UX

Global: Left nav (Inventory, Check-In/Out, Transfers, Calibration, Kits, Reports, Admin). Top bar: Search, Site selector, Profile, Theme toggle.

Dashboard Cards:

Calibrations due (7/30/60d) with drill-through

Quarantine queue

Pending approvals (transfers, overrides)

Overdue returns

Scanning Mode: Single-purpose full-screen view; barcode focus by default; audible feedback.

History View: Timeline with filter chips; export button.

Accessibility: Full keyboard navigation; focus rings; error states with ARIA live regions.

9) Business Rules

BR-1: A tool cannot be issued if calibration_due_at < now() OR status ∈ {quarantined, out_for_repair, retired}. Manager override requires permission: override.issue.blocked, reason, and becomes an immutable event.

BR-2: Changing status to damaged auto-creates a repair task and removes tool from available lists.

BR-3: Receiving calibration marks status serviceable only if certificate_number + next_due_at are set.

BR-4: Transfers require manager approval when crossing warehouses.

BR-5: Long-term floor assignment uses a logical location (warehouse:zone=floor), with review every 90 days.

10) Reporting

Standard Dashboards: Calibration compliance %, Quarantine aging, Utilization by type/location, Transfer SLA, Exception/override trends.

Ad-Hoc: Metabase with governed models (no raw table access to general users).

Scheduling: Email PDFs to role groups; export CSV with column filters.

Data Retention: Reports & snapshots retained 7 years; raw audit indefinitely or as per policy.

11) Security, Privacy, & Compliance

AuthN/Z: OIDC/SAML SSO; short-lived JWTs; refresh rotation; least-privilege RBAC + capability toggles.

Data Protection: Encryption in transit (TLS), at rest (KMS). Field-level encryption for phone numbers.

Secrets: Vault or AWS Secrets Manager.

Hardening: CIS benchmarks for containers/hosts; read-only file systems for stateless apps; WAF on edge.

Compliance Orientation: Aerospace MRO expectations (e.g., AS9100 practices), tool control traceability, calibration record retention, and full auditability.

12) Performance, Scale & Sizing

Initial target: 100k tools, 1k concurrent users, 10 sites.

Read scaling via read replicas; write primarily in single region; async replication to DR region.

Caching: Redis for session tokens, hot lookups (tool by serial/tool number).

13) Offline Strategy & Sync

Client: PWA caches UI + API mutations; local SQLite stores pending ops and a subset of inventory for the site.

Conflict Resolution:

Checkouts: server-wins with operator prompt if mismatch.

Metadata (notes/photos): last-writer-wins with audit.

Sync Transport: Background sync jobs; exponential backoff.

Verification: Daily heartbeat to ensure client clocks aren’t drifting.

14) CI/CD & Environments

Envs: dev → stage → prod.

CI: Lint, typecheck, unit tests, SAST, build docker images, SBOM.

CD: Blue/green or canary; DB migrations via Prisma migrate (guarded).

Feature Flags: ConfigCat/Unleash for dark launches.

15) Monitoring, Alerts & Runbooks

SLOs: API availability 99.9%; p95 latency < 300ms (checkout).

Dashboards: API latency, error rates, queue depth, DB health, sync backlog.

Alerts: Calibration scheduler failures, growing quarantine backlog, image upload errors, sync conflicts > threshold.

Runbooks:

“Blocked Checkout” diagnostics

“Sync Conflicts Surge” mitigation

“Label Printer Offline” steps (local agent restart, queue flush)

16) Testing Strategy

Unit: Domain services (checkout rules, calibration due calculators).

Integration: API + DB, auth, S3 uploads, ZPL print adapters.

E2E: Cypress/Playwright scripted flows: checkout, quarantine, calibration receive, transfer.

Performance: k6 or Artillery; simulate 1k concurrent checkouts.

Security: SAST/DAST; dependency scanning; periodic pentest.

UAT: Tool Room pilot in one site for 2 weeks.

17) Acceptance Criteria (Condensed per FR)

FR-1: Issuing a blocked tool shows reason; Manager override requires permission + reason; audit event created; checkout completes.

FR-2: Marking “Damaged” immediately removes tool from available list and creates repair task.

FR-3: Calibration receive requires cert + next due date; status flips to serviceable; history updated.

FR-4: Transfer approval changes location; history shows from→to; blocking issuance during in-transit state.

FR-5: Theme persists; dashboard loads < 2s with live counts.

FR-6: Uploads generate thumbnails and display within 2s on tool page.

FR-7: History timeline filters by event type/date and exports CSV.

FR-8: Duplicate serial per type rejected; system-assigned serial has prefix (e.g., SYS-{uuid}) and prints label.

FR-9: Capability toggles take effect without deploy; unauthorized actions 403 with error code.

FR-10: With network down, local issuance still records; on reconnect, server shows the event and resolves conflicts.

FR-11: Each standard report renders < 5s for 100k tools; schedules send PDFs.

18) Deployment & Infrastructure

Cloud: AWS (recommended):

EKS (Kubernetes) or ECS Fargate for services

RDS/Aurora Postgres

S3 + CloudFront for assets

OpenSearch or Loki for logs

ALB + WAF

Secrets: AWS Secrets Manager

Backups:

Postgres: PITR + daily snapshots (retain 35 days)

S3 versioning + lifecycle to glacier for certs/photos

Config & manifests in Git (IaC: Terraform)

19) Data Retention & Backup

Calibration records/certs: ≥ 7 years

Audit logs: ≥ 7 years (tamper-evident)

Operational logs: 30–90 days hot; 1 year cold

User accounts: Deactivated (not deleted) for traceability

20) Risk Register (Selected)
ID	Risk	Probability	Impact	Mitigation
R1	Offline sync conflicts create issuance ambiguity	M	H	Server-wins for checkout; conflict queue; operator reconciliation UI
R2	Label printer ecosystem variability	M	M	Support network ZPL + local agent fallback; pre-flight printer test
R3	Photo/cert storage growth	M	M	S3 lifecycle rules; thumbnailing; quotas
R4	SSO integration differences	M	M	Use standards (OIDC/SAML); Keycloak adapters; sandbox with each IdP
R5	Calibration vendor delays	M	M	SLA monitoring, reminders, escalations, alternate vendors
21) Rollout Plan

Alpha (1 site, limited roles): Check-in/out, basic history, photos.

Beta (3 sites): Calibration workflows, transfers, reports, label printing.

GA: Full RBAC, offline sync, Fly Away Kits, enterprise reporting.

22) Open Questions

Required minimum calibration retention (policy may exceed 7 years)?

Specific label sizes/printer models at each site?

Do we need multilingual UI for overseas sites?

Any regulatory wording required on certificates storage metadata?

23) Glossary

MRO: Maintenance, Repair, and Overhaul.

RBAC: Role-Based Access Control.

Fly Away Kit: Mobile tool set taken off-site for remote work.

Quarantine: Non-issuable state pending repair/calibration.

24) Appendix A — Role & Permission Matrix (excerpt)
Capability	User	Manager	Admin
Checkout serviceable tool	✓	✓	✓
Override blocked issuance	✗	✓	✓
Approve transfers	✗	✓	✓
Manage users/roles	✗	✗	✓
View compliance reports	✓	✓	✓
Configure calibration intervals	✗	✓	✓
25) Appendix B — Example ZPL (Label)
^XA
^FO50,50^A0N,30,30^FDTool#: ^FS
^FO50,90^A0N,30,30^FD{TOOL_NUMBER}^FS
^FO50,130^A0N,30,30^FDSerial: {SERIAL}^FS
^FO50,170^A0N,30,30^FDDue: {CAL_DUE}^FS
^FO50,220^BY2
^BCN,100,Y,N,N
^FD{BARCODE_DATA}^FS
^XZ

Sign-off

Product: __________

Engineering: __________

Quality/Compliance: __________

Operations: __________

Date: __________
