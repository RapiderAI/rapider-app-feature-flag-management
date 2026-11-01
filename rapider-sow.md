<!-- Rapider Official App SOW & Specification | App #105: Feature Flag Management (feature-flag-management) | Domain: DEV -->

# #105 Feature Flag Management (`feature-flag-management`) — Scope of Work (SOW)

- **App Number & Name:** `#105 Feature Flag Management`
- **Proposed App Key:** `feature-flag-management`
- **Domain / Suite & Model Prefix:** `DEV` (`Dev*`) — Category `11. Software Development & DevOps`
- **Representative Benchmark (`P1`):** `LaunchDarkly`
- **Combined Feature Catalog:** [../combined-features/105-feature-flag-management-features.md](../combined-features/105-feature-flag-management-features.md)

---

## 1. Executive Outcome & Target Audience

**Feature Flag Management (`feature-flag-management`)** decouples code deployment from feature release across web, mobile, backend, and AI-powered applications in the Rapider `DEV` suite. Engineering, product, and SRE teams ship dark code safely to `main`, progressively ramp traffic from `0.1%` internal dogfooders to `100%` general availability, dynamically update JSON and AI model/prompt configurations at runtime, and trigger sub-second emergency kill switches when downstream errors spike—without hotfix builds or redeployments.

- **SME & Startup Fast-Path:** A small product team creates a boolean `DevFeatureFlag` in `flag-drawer`, copies an OpenFeature-compatible SDK key from `dev` and `prod` (seeded out-of-the-box), toggles features on for internal testers, and dials a percentage slider from `10%` to `100%`—no complex approval pipelines or multi-stage templates required until enabled.
- **Enterprise Progressive Disclosure:** Regulated and multi-squad organizations unlock multi-context targeting (`user`, `organization`/`CrmAccount`, `device`), reusable `DevFlagSegment` cohorts, 4-eyes `DevFlagChangeRequest` approvals routed via Rapider Home `WorkItem`s, progressive `DevFlagRolloutPlan` schedules guarded by `#104` crash-free stability gates, immutable `DevFlagRevision` SOC2 audit trails, and automated Git `DevFlagCodeRef` technical-debt cleanup tracking.
- **Measurable Business & Engineering Outcomes:**
  1. Reduce production rollback recovery time from **30+ minutes (CI/CD redeploy)** to **under 5 seconds** via operational kill switches and guarded auto-rollbacks.
  2. Increase deployment frequency by **2x–4x** by enabling trunk-based development behind unexposed `RELEASE` flags linked to `ApmIssue` / `ApmEpic` items.
  3. Eliminate **80%+ stale flag technical debt** via automated `POTENTIALLY_STALE` detection, expiration TTL alerts, and exact Git file/line code references.
- **Top 6 Global Benchmarks Synthesized (`P1`–`P6`):** `LaunchDarkly` (`P1` — Representative Product), `Statsig` (`P2`), `Split / Harness` (`P3`), `Unleash Enterprise` (`P4`), `Microsoft Azure App Configuration` (`P5`), and `Atlassian Jira & Compass Feature Flags` (`P6`).

---

## 2. In-Scope, Out-of-Scope & Suite Ownership Boundaries

### In-Scope
- Defining typed flags (`BOOLEAN`, `STRING`, `NUMBER`, `JSON`) across `RELEASE`, `EXPERIMENT`, `OPS_KILL_SWITCH`, `PERMISSION`, and `AI_CONFIG` categories with prerequisite flag chains (`FFM-001`, `FFM-004`, `FFM-005`).
- Managing isolated `DevFlagEnvironment` configurations, separate server-side secret vs. client-side publishable SDK keys, and CNCF OpenFeature evaluation contracts (`FFM-002`, `FFM-003`).
- Multi-context rule targeting, reusable `DevFlagSegment` cohorts, sticky `Decimal128` percentage rollouts, mutually exclusive allocation layers, and an interactive Evaluation Simulator (`FFM-006`–`FFM-010`, `FFM-023`).
- Instant kill switches, scheduled changes, multi-stage `DevFlagRolloutPlan` ramps, `#104`-linked Guarded Releases, and immutable `DevFlagRevision` snapshots with 1-click rollback (`FFM-011`–`FFM-014`).
- Protected-environment 4-eyes `DevFlagChangeRequest` workflows via Home `WorkItem`s, evaluation telemetry, stale flag lifecycle governance, Git `DevFlagCodeRef` indexing, and grounded AI rule/cleanup assistants (`FFM-015`–`FFM-022`, `FFM-024`–`FFM-028`).

### Out-of-Scope
- Full data-warehouse funnel analytics, cohort retention charts, and multi-metric statistical warehouse queries (owned by `#097 Product Analytics`, fed via `#105` exposure events).
- Application exception stack-trace symbolication and session replay storage (owned by `#104 Application Error Tracking`).
- Infrastructure secret vault storage for database credentials, TLS certificates, or API private keys (owned by Platform Secret Manager; `#105` flags NEVER store plaintext credentials).
- Customer billing, plan invoicing, and subscription checkout execution (owned by `FIN` / `COM`; `#105` reads `CrmAccount`/`FinSubscription` tier attributes for entitlement gating).

### Cross-Suite Ownership & Integration Contract

| Domain / App | Owns (Canonical System of Record) | Integration Contract with `#105` (`feature-flag-management`) |
| :--- | :--- | :--- |
| **`#105` (`feature-flag-management`)** | `DevFeatureFlag`, `DevFlagEnvironment`, `DevFlagRuleSet`, `DevFlagSegment`, `DevFlagRolloutPlan`, `DevFlagChangeRequest`, `DevFlagRevision`, `DevFlagCodeRef` | Evaluates runtime flag manifests, emits `dev.flag.toggled.v1` and exposure events, and enforces environment governance. |
| **`#104` (`application-error-tracking`)** | `DevErrorIssue`, `DevErrorEvent`, `DevReleaseHealth` | `#104` attaches active flag variants to crash events and emits `dev.error.release_stability.breached.v1`; `#105` subscribes to auto-pause/rollback Guarded Releases. |
| **`APM` (`planning` / `apm`) & `#108`** | `ApmProject`, `ApmEpic`, `ApmIssue`, `DevRoadmapItem` | `DevFeatureFlag` links `linkedApmIssueIds` / `linkedApmEpicId` so Agile boards and Roadmaps display real-time per-environment rollout % without duplicating issues. |
| **`CRM` & `FIN` (`crm`, `billing`)** | `CrmAccount`, `FinSubscription` | `#105` resolves B2B organization tier/plan attributes against canonical `CrmAccount` IDs for `PERMISSION` flags. |
| **`#097` (`product-analytics`)** | Product events, funnels, statistical lift reports | `#105` streams anonymized `dev.flag.evaluated_exposure.v1` events to `#097` for downstream A/B conversion analysis. |
| **Platform Core Services** | Identity/RBAC, Secret Manager, Home `WorkItem`s, `projectChannelMessage` | Production 4-eyes `DevFlagChangeRequest` reviews and Guarded Release rollbacks surface as Home `WorkItem`s; AI & team discussions use `projectChannelMessage`. |

---

## 3. Primary Web Pages & Sidebar Navigation Architecture (12 Pages)

Every visible sidebar section strictly enforces the **Minimum 3 Items Rule** (`Release Control` = 3 items, `Targeting & Rollouts` = 3 items, `Governance & Lifecycle` = 3 items; detail and drawer routes set `displayOnMenu: false`).

| # | Route | Page Title | Sidebar Section | Menu Visible | Key Components & UX Standards |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `dashboard` | Flag Control Overview | `Release Control` | `true` (Order 1) | KPI cards (`countSWR`/`findSWR`) for Active Flags, Active Guarded Rollouts, Pending `prod` Change Requests, and Stale Flags; recent `DevFlagRevision` feed and Emergency Kill-Switch quick panel. |
| 2 | `flags` | Feature Flags & Toggles | `Release Control` | `true` (Order 2) | Dual Table + `<rapider-kanban>` Lifecycle Board (`DRAFT`, `LIVE`, `POTENTIALLY_STALE`, `READY_TO_CLEANUP`, `ARCHIVED`); inline per-environment toggle pills; `#activeFilterTagsBar` with `Clear all`; clickable flag key/name (`flag-detail`). |
| 3 | `ai-configs` | Dynamic & AI Configs | `Release Control` | `true` (Order 3) | Specialized catalog of `JSON` and `AI_CONFIG` flags governing runtime model IDs, `Decimal128` temperatures, and prompt template versions with schema validation badges. |
| 4 | `segments` | Audience Segments | `Targeting & Rollouts` | `true` (Order 4) | `DevFlagSegment` table managing reusable inclusion/exclusion cohorts and attribute predicates (`CrmAccount` tier, region, semver), showing count of referencing flags. |
| 5 | `rollout-plans` | Guarded Rollout Schedules | `Targeting & Rollouts` | `true` (Order 5) | Active & scheduled `DevFlagRolloutPlan` progressive ramps (`1% -> 10% -> 50% -> 100%`) displaying live `#104` stability guardrail status and Pause / Rollback actions. |
| 6 | `simulator` | Evaluation Playground | `Targeting & Rollouts` | `true` (Order 6) | Interactive context builder (`user`, `organization`, `device`, `appVersion`) evaluating live or draft `DevFlagRuleSet` rules side-by-side across environments with matched-clause trace output. |
| 7 | `change-requests` | Change Requests & Approvals | `Governance & Lifecycle` | `true` (Order 7) | 4-eyes `DevFlagChangeRequest` queue (`PENDING_REVIEW`, `APPROVED`, `APPLIED`, `REJECTED`) with `<rapider-kanban>` view, rule diff preview, and Home `WorkItem` status sync. |
| 8 | `flag-debt` | Stale Flags & Code Refs | `Governance & Lifecycle` | `true` (Order 8) | Technical-debt hygiene view listing expired TTL flags, `100%` single-variation flags, and `DevFlagCodeRef` Git repository file/line occurrences with 1-click `ApmIssue` cleanup creation. |
| 9 | `environments` | Environments & SDK Keys | `Governance & Lifecycle` | `true` (Order 9) | `DevFlagEnvironment` configuration (`dev`, `qa`, `staging`, `prod`), approval-required locks, masked SDK key rotation, OpenFeature snippet generator, and audit export. |
| 10 | `flag-detail` | Flag Targeting & History | *(Hidden)* | `false` | Environment switcher tabs, prerequisite selector, `IF/ELSE` rule builder, `Decimal128` percentage split sliders, variation evaluation chart, `ApmIssue` links, `DevFlagRevision` diff rollback, and AI assistant (`projectChannelMessage`). |
| 11 | `change-request-detail` | Change Request Diff Review | *(Hidden)* | `false` | Side-by-side before/after rule and weight comparison, impact assessment, mandatory reviewer comment, and guarded **Approve & Apply** / **Reject** buttons. |
| 12 | `flag-drawer` | Create / Edit Feature Flag | *(Hidden)* | `false` | Sliding form drawer for `DevFeatureFlag` metadata and variations; enforces `try/catch/finally` button state reset, SWR invalidation, broadcast, and auto-close on Save/Cancel. |

**Mandatory Rapider UI Compliance across all 12 Pages:**
- Initial data loads use `window.rapiderApi.findSWR` and `countSWR` with zero redundant `isDeleted` filter clauses; local arrays filter `item.isDeleted !== true` and update optimistically on toggle/edit/delete.
- Tables never include a "View" button in `Actions`; clicking `flagKey`/`name` or `changeRequestTitle` navigates to `flag-detail` / `change-request-detail`; `Edit` is icon + text and `Delete` is icon-only with `aria-label="Delete"`.
- Primary and destructive confirmation buttons enforce `#ffffff !important` text contrast; native `alert`/`confirm`/`prompt` dialogs are strictly banned (kill-switch and delete confirmations use styled inline Rapider modals `#rapiderConfirmOkBtn`).

---

## 4. Canonical Data Models (`Dev*` — 8 Models)

All models omit `projectId`, use backend-generated UUIDs (`00000000-xxxx-xxxx-xxxx-xxxxxxxxxxxx` in deterministic seed fixtures), enforce optimistic `version` checks, and store exact weights/thresholds as `Decimal128`.

| Model Name | Key Fields & Types | Relationships & External Refs | Invariants & Lifecycle |
| :--- | :--- | :--- | :--- |
| **`DevFeatureFlag`** | `id` (UUID), `key` (String, immutable `kebab-case`), `name` (String), `description` (String), `category` (`RELEASE` \| `EXPERIMENT` \| `OPS_KILL_SWITCH` \| `PERMISSION` \| `AI_CONFIG`), `valueType` (`BOOLEAN` \| `STRING` \| `NUMBER` \| `JSON`), `variations` (JSON[]), `defaultOnVariationKey` (String), `defaultOffVariationKey` (String), `isPermanent` (Boolean), `expiresAt` (DateTime?), `lifecycleStage` (`DRAFT` \| `LIVE` \| `POTENTIALLY_STALE` \| `READY_TO_CLEANUP` \| `ARCHIVED`), `tags` (String[]), `version` (Integer) | Refs `ownerUserId` (Platform Identity), `linkedApmEpicId` (`ApmEpic`), `linkedApmIssueIds` (`ApmIssue[]`), `threadChannelId` (`projectChannelMessage`) | `key` unique per workspace and immutable once created (prevents breaking deployed SDK calls); contains `>= 2` uniquely keyed `variations` matching `valueType`; plaintext secrets rejected by validator. |
| **`DevFlagEnvironment`** | `id` (UUID), `key` (String, e.g., `dev`, `staging`, `prod`), `name` (String), `colorHex` (String), `isProduction` (Boolean), `requireChangeApproval` (Boolean), `minApprovalsRequired` (Integer), `serverSdkKeyHash` (String), `clientPublishableKey` (String), `sortOrder` (Integer), `version` (Integer) | Referenced by `DevFlagRuleSet`, `DevFlagRolloutPlan`, `DevFlagChangeRequest` | `key` unique per workspace; `serverSdkKeyHash` stored hashed after initial display; if `requireChangeApproval = true`, direct `updateRuleSet` mutations are rejected unless executed via an `APPROVED` `DevFlagChangeRequest` or audited emergency kill-switch break-glass. |
| **`DevFlagRuleSet`** | `id` (UUID), `flagId` (UUID), `environmentId` (UUID), `isEnabled` (Boolean), `prerequisites` (JSON[]), `individualTargets` (JSON), `targetingRules` (JSON[]), `fallthroughAllocation` (JSON — variation `Decimal128` weights), `offVariationKey` (String), `bucketingSalt` (String), `evaluationCount24h` (Long), `lastEvaluatedAt` (DateTime?), `version` (Integer) | Belongs to `DevFeatureFlag` and `DevFlagEnvironment`; rules may ref `DevFlagSegment` IDs and `CrmAccount` tier attributes | Unique `(flagId, environmentId)`; acyclic `prerequisites` graph (no circular flag dependencies); every percentage split `fallthroughAllocation` or rule rollout has `Decimal128` `weightPercent` values summing to exactly `100.0000`. |
| **`DevFlagSegment`** | `id` (UUID), `key` (String), `name` (String), `contextKind` (`USER` \| `ACCOUNT` \| `DEVICE` \| `CUSTOM`), `includedKeys` (String[]), `excludedKeys` (String[]), `matchClauses` (JSON[]), `referencedFlagCount` (Integer), `version` (Integer) | Referenced by `DevFlagRuleSet.targetingRules`; `ACCOUNT` keys align with canonical `CrmAccount.id` | `key` unique per workspace; cannot be deleted while `referencedFlagCount > 0`. |
| **`DevFlagRolloutPlan`** | `id` (UUID), `flagId` (UUID), `environmentId` (UUID), `targetVariationKey` (String), `stages` (JSON[] — `{ weightPercent: Decimal128, durationMinutes: Integer }`), `currentStageIndex` (Integer), `guardrailServiceId` (UUID?), `minCrashFreeRate` (`Decimal128`?), `status` (`SCHEDULED` \| `ACTIVE` \| `PAUSED_GUARDRAIL` \| `COMPLETED` \| `ROLLED_BACK` \| `CANCELLED`), `startedAt` (DateTime?), `version` (Integer) | Belongs to `DevFeatureFlag` & `DevFlagEnvironment`; optional `guardrailServiceId` refs `#104` `DevErrorService` | At most one `ACTIVE` rollout plan per `(flagId, environmentId)`; if `#104` stability drops below `minCrashFreeRate`, transitions to `PAUSED_GUARDRAIL` or `ROLLED_BACK` and creates a Home `WorkItem`. |
| **`DevFlagChangeRequest`** | `id` (UUID), `title` (String), `flagId` (UUID), `environmentId` (UUID), `requestedByUserId` (UUID), `proposedRuleSetPatch` (JSON), `reasonComment` (String), `scheduledApplyAt` (DateTime?), `status` (`PENDING_REVIEW` \| `APPROVED` \| `APPLIED` \| `REJECTED` \| `CANCELLED`), `approverUserIds` (UUID[]), `homeWorkItemId` (UUID?), `version` (Integer) | Belongs to `DevFeatureFlag` & `DevFlagEnvironment`; `homeWorkItemId` refs Platform Home `WorkItem` | Self-approval is prohibited (`requestedByUserId` cannot be in `approverUserIds` when `minApprovalsRequired >= 1`); applying validates `expectedVersion` of target `DevFlagRuleSet` to prevent stale overwrites. |
| **`DevFlagRevision`** | `id` (UUID), `flagId` (UUID), `environmentId` (UUID?), `revisionNumber` (Long), `actorUserId` (UUID), `changeType` (`CREATED` \| `TOGGLED` \| `RULES_UPDATED` \| `KILL_SWITCH` \| `ROLLBACK` \| `ARCHIVED`), `reasonComment` (String), `previousSnapshot` (JSON), `newSnapshot` (JSON), `createdAt` (DateTime) | Belongs to `DevFeatureFlag` | **Strictly append-only immutable audit record**; restoring a past revision creates a new `revisionNumber + 1` with `changeType = ROLLBACK`. |
| **`DevFlagCodeRef`** | `id` (UUID), `flagId` (UUID), `repositoryName` (String), `branchName` (String), `commitSha` (String), `filePath` (String), `lineNumber` (Integer), `codeSnippet` (String), `scannedAt` (DateTime), `version` (Integer) | Belongs to `DevFeatureFlag`; `repositoryName` aligns with `#099` Git repository | Replaced atomically per `(flagId, repositoryName, branchName)` scan run; snippet length capped and sanitized. |

---

## 5. Core End-to-End User Journeys

1. **SME Fast-Path Flag Creation & Instant Dev/Staging Toggle:**
   - Engineer opens `#/flags`, clicks **New Feature Flag** (`flag-drawer`), enters `one-click-reorder` (`BOOLEAN`, `RELEASE`), links `ApmIssue` `APM-218`, and clicks **Save** (`DevFeatureFlag` + default `DevFlagRuleSet` rows created across environments; drawer closes; table & `<rapider-kanban>` update optimistically).
   - Engineer toggles `dev` to `ON` directly from the `#/flags` row pill, tests in `#/simulator` with `{ "user": { "id": "u-42", "plan": "PRO" } }`, and confirms `true` is served via matched rule trace.
2. **Enterprise 4-Eyes Production Change Request via Home `WorkItem`:**
   - On `#/flag-detail` (`prod` tab, where `requireChangeApproval = true`), the Product Engineer configures a `25.0000%` `Decimal128` rollout for segment `EU Enterprise Accounts` and clicks **Request Production Change**.
   - `submitChangeRequest` creates `DevFlagChangeRequest` (`PENDING_REVIEW`) and emits a Rapider Home `WorkItem` to the Release Lead.
   - Release Lead clicks the Home `WorkItem` deep link into `#/change-request-detail`, inspects the side-by-side rule diff, enters review notes, and clicks **Approve & Apply**—atomically updating `DevFlagRuleSet`, recording `DevFlagRevision`, and broadcasting SSE updates to SDKs.
3. **Guarded Progressive Rollout & Automated `#104` Error Rollback:**
   - Team starts a `DevFlagRolloutPlan` (`1% -> 10% -> 50% -> 100%`) bound to `#104` `Checkout API` (`minCrashFreeRate = 99.2000%`).
   - At the `10%` stage, `#104` emits `dev.error.release_stability.breached.v1` (`97.8000%` crash-free). `#105` immediately transitions the plan to `ROLLED_BACK`, reverts `DevFlagRuleSet` to `0%` (`offVariationKey`), logs an immutable `DevFlagRevision`, and raises an urgent Home `WorkItem`.
4. **Stale Flag Debt Cleanup & `ApmIssue` Handoff:**
   - Scheduled hygiene workflow marks `legacy-cart-v1` as `POTENTIALLY_STALE` (serving `100%` `true` for 45 days with 0 rule changes).
   - Engineering Lead opens `#/flag-debt`, inspects 4 remaining `DevFlagCodeRef` occurrences in `checkout-web`, asks Rapider AI on `projectChannelMessage` to draft a cleanup checklist (`FFM-027`), and clicks **Create Cleanup Issue**—generating a linked `ApmIssue` before moving the flag to `ARCHIVED`.
5. **Search, Active Filter Tags, Click-Through & State Restoration:**
   - On `#/flags`, user searches `"checkout"` and filters `Category: OPS_KILL_SWITCH` + `Lifecycle: LIVE`. `#activeFilterTagsBar` displays `Search: "checkout" ✕`, `Category: "OPS_KILL_SWITCH" ✕`, `Lifecycle: "LIVE" ✕`, and `Clear all`.
   - Clicking `checkout-stripe-fallback` opens `#/flag-detail?id=...` cleanly; clicking `#btnGoBack` returns to `#/flags` with all filter tags and search preserved; clicking `Clear all` restores the full flag list.

---

## 6. Consolidated Feature Priorities & Work Packages

### `P0` Foundation (MVP Release-Ready Core)
- **WP-P0-1: Flag Registry, Environments & OpenFeature SDK Keys (`FFM-001`–`FFM-004`):** `DevFeatureFlag`, `DevFlagEnvironment`, `DevFlagRuleSet` schemas, acyclic prerequisite validation, key generation/rotation, `#/flags`, `flag-drawer`, `#/environments`.
- **WP-P0-2: Context Targeting, Segments, Sticky `Decimal128` Splits & Simulator (`FFM-006`–`FFM-009`):** `DevFlagSegment` management (`#/segments`), `IF/ELSE` rule builder & exact `Decimal128` weight sliders (`#/flag-detail`), deterministic MurmurHash3/SHA-256 bucketing, and `#/simulator`.
- **WP-P0-3: Emergency Kill Switches, 4-Eyes Approvals & Audit Revisions (`FFM-011`, `FFM-015`–`FFM-017`):** One-click kill switch, `DevFlagChangeRequest` + Home `WorkItem` integration (`#/change-requests`, `#/change-request-detail`), append-only `DevFlagRevision`, and `ApmIssue`/`ApmEpic` linking.
- **WP-P0-4: Evaluation Telemetry & Stale Lifecycle Hygiene (`FFM-019`, `FFM-020`):** `#/dashboard`, variation evaluation counters, TTL expiration warnings, and `<rapider-kanban>` lifecycle board.

### `P1` Enterprise Scale & Progressive Delivery
- **WP-P1-1: Dynamic AI Configs & B2B Entitlement Context (`FFM-005`, `FFM-010`):** `#/ai-configs` JSON schema & model/temperature governance, plus `CrmAccount`/`FinSubscription` tier attribute resolution.
- **WP-P1-2: Progressive Ramp Schedules, `#104` Guarded Releases & Rollback (`FFM-012`–`FFM-014`, `FFM-018`):** `DevFlagRolloutPlan` stage scheduler (`#/rollout-plans`), `#104` stability event subscriber, 1-click `DevFlagRevision` rollback, and environment-scoped RBAC.
- **WP-P1-3: Git Code References & `#097` Analytics Exposure Stream (`FFM-021`, `FFM-022`):** `DevFlagCodeRef` ingestion & `#/flag-debt` explorer, plus anonymized exposure event streaming to `#097`.

### `P2` Adjacent Capabilities & Grounded AI
- **WP-P2-1: Mutually Exclusive Layers, GitOps Sync & Context Hashing (`FFM-023`–`FFM-025`):** Experimentation allocation layers, declarative manifest import/export (respecting `prod` approval gates), and SDK attribute hashing policies.
- **WP-P2-2: Grounded AI Rule Author, Debt Cleaner & Guardrail Explainer (`FFM-026`–`FFM-028`):** Natural-language-to-rule draft proposals, dead-branch `ApmIssue` generator, and guarded-release anomaly explanations on `projectChannelMessage`.

---

## 7. Roles, Permissions & Field-Level Governance

| Role | Permitted Actions | Restricted / Field-Level Boundaries |
| :--- | :--- | :--- |
| **Developer** | Create `DevFeatureFlag` (`DRAFT`/`LIVE`); edit targeting rules and toggle flags in non-production environments (`dev`, `qa`, `staging`); run `#/simulator`; submit `DevFlagChangeRequest` for `prod`; upload `DevFlagCodeRef` scans. | Cannot directly mutate `DevFlagRuleSet` in environments where `requireChangeApproval = true`; cannot self-approve own `DevFlagChangeRequest`; cannot view raw `serverSdkKey` secrets after creation. |
| **Release Lead / SRE Approver** | All Developer actions + approve/reject `prod` `DevFlagChangeRequest` items (`Home WorkItem`), start/pause `DevFlagRolloutPlan` schedules, execute audited emergency `OPS_KILL_SWITCH` toggles in `prod`, and restore `DevFlagRevision` snapshots. | Cannot delete or alter append-only `DevFlagRevision` audit logs; cannot bypass secret-scanning validation on JSON/AI Config payloads. |
| **Product Manager / QA** | View all flags, rollout percentages, `ApmEpic`/`ApmIssue` links, `#/simulator`, and `DevFlagSegment` definitions; submit `prod` `DevFlagChangeRequest` drafts. | Cannot rotate SDK keys, modify `DevFlagEnvironment` security locks, or archive flags with non-zero 24h evaluation traffic. |
| **Workspace Governance Admin** | Create/lock `DevFlagEnvironment` definitions, set `minApprovalsRequired`, rotate server/client SDK keys, configure context-hashing privacy rules, and export SOC2 audit trails. | Subject to immutable `DevFlagRevision` logging on every administrative policy change. |
| **SDK / Edge Evaluation Identity** | Read-only streaming/polling access to rule manifests (`server` key) or single-context evaluation (`client` key) + write-only aggregated evaluation counters. | Client publishable keys NEVER receive full `DevFlagSegment` inclusion lists or other customers' `CrmAccount` IDs. |

---

## 8. Technical APIs, Guarded Commands, Events & `requiredWorkflows`

### Guarded Domain Commands
Every mutating command validates RBAC, `idempotencyKey`, `correlationId`, and `expectedVersion`:
- `evaluateFlags(envKey, contextPayload)` -> Deterministic sub-millisecond evaluation verifying prerequisite DAGs, individual targets, `DevFlagSegment` matches, and `Decimal128` MurmurHash3/SHA-256 percentage buckets.
- `updateRuleSet(flagId, environmentId, patch, reasonComment, expectedVersion, idempotencyKey)` -> Enforces `Decimal128` weight sum `== 100.0000`, acyclic prerequisites, and `!requireChangeApproval`, appending `DevFlagRevision`.
- `submitChangeRequest(flagId, environmentId, proposedPatch, reasonComment, scheduledApplyAt?, idempotencyKey)` -> Creates `DevFlagChangeRequest` and dispatches shared Home `WorkItem`.
- `decideChangeRequest(changeRequestId, decision, reviewComment, expectedVersion, idempotencyKey)` -> Enforces 4-eyes non-self-approval (`requestedByUserId != actorUserId`), applies patch atomically, and closes Home `WorkItem`.
- `triggerEmergencyKillSwitch(flagId, environmentId, reasonComment, idempotencyKey)` -> Immediate break-glass disablement to `offVariationKey` with high-priority audit revision and SSE broadcast.
- `rollbackToRevision(flagId, environmentId, targetRevisionNumber, reasonComment, expectedVersion, idempotencyKey)` -> Restores historical rule snapshot as a new monotonic version.

### Kafka Domain Events (Published & Consumed)
- **Published:** `dev.flag.created.v1`, `dev.flag.toggled.v1`, `dev.flag.rules_updated.v1`, `dev.flag.killswitch_triggered.v1`, `dev.flag.rollout_stage_advanced.v1`, `dev.flag.evaluated_exposure.v1` (anonymized stream for `#097`).
- **Consumed:** `dev.error.release_stability.breached.v1` & `dev.error.killswitch.recommended.v1` (from `#104` to pause/rollback active `DevFlagRolloutPlan`), `apm.issue.status_changed.v1` (notifies flag owner when linked story ships).

### `requiredWorkflows` in `app.manifest.json`
1. `dev-flag-progressive-rollout-scheduler-wf` (Advances `DevFlagRolloutPlan` stages on timer milestones and executes scheduled `DevFlagChangeRequest` activations).
2. `dev-flag-guarded-release-watcher-wf` (Correlates `#104` crash/error spikes against active rollouts, executing automatic pause/rollback and creating Home `WorkItem`s).
3. `dev-flag-stale-debt-scanner-wf` (Daily scan identifying expired TTLs and `100%` static flags with zero rule edits, transitioning `lifecycleStage -> POTENTIALLY_STALE`).
4. `dev-flag-ai-copilot-wf` (Grounded AI rule drafter, conflict analyzer, and cleanup checklist generator on `projectChannelMessage`—never mutating `DevFlagRuleSet` without explicit human submission/approval).

---

## 9. Security, Privacy, Compliance & Non-Functional Requirements

- **Client-Key Zero-Leakage Guarantee:** Browser and mobile publishable SDK endpoints return *only* the evaluated variation values for the caller's single context—never exposing internal `DevFlagSegment` customer lists, unreleased enterprise account names, or competitor-visible rule logic.
- **Secret Injection Prevention & PII Context Hashing (GDPR / SOC2):** Server-side validators scan `variations` and `AI_CONFIG` JSON payloads to reject API private keys, connection strings, or bearer tokens (directing users to Platform Secret Manager). Targeting evaluation supports hashed context attributes (`SHA-256`) so raw customer emails/identifiers never persist in evaluation logs, keeping compliance overhead at \$0 extra infrastructure cost.
- **Immutable SOC2 Change Provenance:** Every toggle, rule edit, approval, and emergency kill switch writes an uneditable `DevFlagRevision` with mandatory `reasonComment` in production environments.
- **Accessibility, Performance & Resilience:** WCAG 2.2 AA accessible toggles, sliders, and diff tables across `390px`, `768px`, and `1440px`; local SDK evaluation `< 1ms` and edge evaluation API `< 25ms` p99; deterministic fallback to default safe variation if network connectivity drops.

---

## 10. Deterministic Acceptance Criteria

1. **Zero `projectId` & Immutable Flag Keys:** All 8 `Dev*` schemas contain zero `projectId` fields, use backend UUIDs (`00000000-...` in fixtures), and reject any attempt to mutate `DevFeatureFlag.key` after creation.
2. **Exact `Decimal128` Percentage Rollout Validation:** Saving a percentage split whose weights sum to `99.9900%` or `100.0100%` is deterministically rejected; `33.3333% + 33.3333% + 33.3334% = 100.0000%` succeeds and buckets identical `(flagKey, salt, userKey)` inputs to the exact same variation across 10,000 evaluations.
3. **Acyclic Prerequisite Enforcement:** Attempting to save `Flag A -> requires Flag B` when `Flag B -> requires Flag A` returns a validation error preventing infinite evaluation loops.
4. **4-Eyes Production Lock & Self-Approval Ban:** Direct `updateRuleSet` calls on an environment with `requireChangeApproval = true` fail with `403 CHANGE_REQUEST_REQUIRED`; submitting `DevFlagChangeRequest` creates a shared Home `WorkItem`, and attempting to approve one's own request fails with `403 SELF_APPROVAL_FORBIDDEN`.
5. **Client Publishable Key Isolation:** Calling the client evaluation endpoint returns evaluated key-value pairs for the supplied user context and `0` rule clauses or segment inclusion arrays in the HTTP payload.
6. **Guarded Release Auto-Rollback (`#104` Integration):** Emitting `dev.error.release_stability.breached.v1` below `DevFlagRolloutPlan.minCrashFreeRate` transitions the active rollout to `ROLLED_BACK`, sets `DevFlagRuleSet` to `offVariationKey`, appends a `DevFlagRevision`, and raises a Home `WorkItem`.
7. **Sidebar Minimum 3 Items Rule:** All 3 visible sidebar sections (`Release Control`, `Targeting & Rollouts`, `Governance & Lifecycle`) each contain 3 visible items (`displayOnMenu: true`), while `flag-detail`, `change-request-detail`, and `flag-drawer` set `displayOnMenu: false`.
8. **SWR Clean Query & Optimistic Toggle/Edit/Delete:** All pages load via `findSWR`/`countSWR` without `isDeleted` filter clauses; toggling an environment pill, editing a segment, or deleting a draft flag immediately updates in-memory arrays and `<rapider-kanban>`, clears SWR `sessionStorage` cache, and rolls back on simulated error.
9. **Table Actions, Contrast & Dialog Ban:** Zero tables show a "View" button in `Actions`; `flagKey` and `title` links navigate to detail routes; `Edit` renders icon + text; `Delete` renders icon-only with `aria-label="Delete"`; primary/danger buttons enforce `#ffffff` text; zero `alert`/`confirm`/`prompt` calls exist.
10. **Active Filter Tags & Clear All:** Filtering `#/flags` by search query, category, or lifecycle stage displays `#activeFilterTagsBar` with HTML-escaped dismissible chips and `Clear all`, synchronously updating both List and `<rapider-kanban>` views.
11. **Drawer Auto-Close & `finally` Reset:** Saving or cancelling `flag-drawer` closes the drawer, broadcasts `RECORD_CREATED`/`RECORD_UPDATED`, and resets button loading state inside `finally`.
12. **Click-Through Navigation & Back State Restoration:** Clicking a flag row on `#/flags` navigates to `#/flag-detail?id=...` without `<base href>` 404 diversion, renders environment rules, and `#btnGoBack` restores `#/flags` with active filter tags intact.
13. **Grounded AI Governance:** Requesting AI targeting rule generation posts a validated JSON rule draft onto `projectChannelMessage` and never modifies `DevFlagRuleSet` without human confirmation/approval.
14. **Theme & Viewport Compliance:** Automated dark/light `validate_theme` contrast checks and responsive layout checks pass cleanly at `390px`, `768px`, and `1440px`.
