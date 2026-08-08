# Answer Key — Phase 5 (Days 121–150)

Trainer prep companion. Answers are grounded in each day's theory section; ⚠ marks
answers drawn from general knowledge beyond the day file — verify against official
docs before teaching.

---

## Day 121 — SRE Principles: SLI/SLO/SLA, Error Budgets, Toil, On-Call

**Warm-up** (from Days 117–120, out of Phase 5 scope — answered from general
FinOps knowledge, ⚠ where not verifiable here):
1. Showback reports team/service cloud costs for visibility without actually
   billing them; chargeback bills those costs to the team's real budget. ⚠
2. Reserved Instances: commit to a specific instance family/region for 1–3
   years — fits steady, predictable workloads. Savings Plans: commit to a
   $/hr compute spend level, flexible across instance families — fits
   variable/mixed workloads. ⚠
3. A FinOps report needs cost allocation by team/service, trend vs budget,
   and savings/rightsizing opportunities — not just a raw spend total. ⚠
4. verify: check Day 120's capstone file for the exact deploy target/cost
   posture recorded there.

**Recall:**
1. SLI: a measured signal of user experience (e.g., latency, error rate).
   SLO: the internal target for an SLI over a window (e.g., 99.5%/30 days).
   SLA: the external, often contractual promise — usually looser than the
   SLO, with a penalty for breach.
2. ≈43 minutes (0.1% × 43,200 minutes in a 30-day window).
3. It should force the team to freeze risky releases and prioritize
   reliability work over new features.
4. Example: log pruning, manual redeploys, or cert renewal done by hand —
   candidates for the toil log.
5. Paging on every log warning causes alert fatigue on non-user-facing
   noise; tying paging to SLO burn ensures a human is interrupted only when
   real user-facing reliability is at risk.

## Day 122 — Prometheus I: Architecture, Exporters, Scrape Config, PromQL Basics

**Warm-up:**
1. SLI is the measured signal; SLO is the numeric target for that SLI over
   a window.
2. ≈216 minutes (~3.6 hours) — 0.5% × 43,200 minutes.
3. Toil is manual, repetitive, automatable work with no enduring value; the
   SRE book caps it near 50% of an SRE's time so the rest goes to
   engineering it away.
4. verify: check the committed `docs/slo.md` from Day 121 for the exact two
   SLIs drafted.

**Recall:**
1. Pull-based — Prometheus scrapes HTTP `/metrics` endpoints on targets at
   an interval.
2. Counter (only increases, e.g. `requests_total`), gauge (up/down, e.g.
   `memory_bytes`), histogram (bucketed observations, e.g. latency),
   summary (client-side quantiles).
3. Host-level metrics (CPU, memory, disk, network) that live outside the
   application process and can't be instrumented from app code.
4. A `ServiceMonitor` (or `PodMonitor`) CR.
5. A histogram's buckets can be aggregated across instances with
   `sum by (le)` before computing a quantile; a summary's quantiles are
   computed client-side per instance and can't be meaningfully aggregated.

## Day 123 — Prometheus II: PromQL Deep, Recording & Alerting Rules

**Warm-up:**
1. A counter only increases (resets on restart); a gauge can go up or down.
2. It tells Prometheus Operator to auto-discover a scrape target.
3. `up{job="X"}` (or `up{instance="X"}`) — returns 1 if up, 0 if down.
4. Because bucket boundaries determine the resolution/accuracy of any
   quantile later computed from them — they must bracket the latencies the
   query actually cares about (e.g., around the SLO threshold).

**Recall:**
1. A counter only ever increases (and resets on restart), so its raw value
   is a cumulative total, not "how much happened in a period" — `rate()`/
   `increase()` are needed for that.
2. `for: 5m` requires the condition to hold continuously for 5 minutes
   before firing — prevents flapping/false pages on transient blips.
3. `histogram_quantile(0.99, sum(rate(request_duration_seconds_bucket[5m])) by (le))`
4. `level:metric:operations` (e.g. `job:http_errors:rate5m`) — exists so
   recording-rule names are self-describing and consistently sortable
   across a large rule set.
5. Prometheus rules decide *when* something is wrong; Alertmanager decides
   *who* hears about it, how, and how often.

## Day 124 — Grafana: Project Dashboard (USE/RED), Variables, Alerting

**Warm-up:**
1. `histogram_quantile(0.99, sum(rate(x_bucket[5m])) by (le))`
2. It saves you from re-running an expensive/frequent query every time —
   the result is precomputed and stored as a new time series.
3. The error ratio `sum(rate(requests_total{code=~"5.."}[5m])) /
   sum(rate(requests_total[5m]))` exceeding 0.05 for 5 minutes.
4. Raw counter values are cumulative totals since start/reset; only
   `rate()` converts them into a meaningful per-second trend for a panel.

**Recall:**
1. USE = Utilization, Saturation, Errors (per-resource, fits
   infrastructure/host panels). RED = Rate, Errors, Duration (per-service,
   fits request-driven services).
2. From a PromQL query, typically `label_values(metric, label)` — Grafana
   runs it against the data source and populates the dropdown.
3. Git-versioned JSON is diffable, reviewable, and recoverable; click-ops
   UI edits aren't tracked or reproducible.
4. The `grafana_dashboard: "1"` label on the ConfigMap.
5. RED panel: Rate = `sum(rate(requests_total[5m]))`. USE panel:
   Utilization = `100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)`.

## Day 125 — Alertmanager: Routing, Silencing, Grouping; Runbooks

**Warm-up:**
1. R = Rate, E = Errors, D = Duration.
2. Label the ConfigMap containing the dashboard JSON `grafana_dashboard:
   "1"` so the Grafana sidecar auto-loads it.
3. 500ms p99 latency, sustained for 5 minutes (`HighLatency`).
4. So changes are version-controlled, diffable, reviewable, and
   recoverable rather than untracked click-ops edits.

**Recall:**
1. Grouping bundles multiple related firing alerts into one notification;
   inhibition suppresses a lower-severity alert entirely while a related
   higher-severity one is already firing.
2. An indefinite silence can permanently hide a real, ongoing problem — an
   expiry scopes the mute to the planned maintenance window only.
3. `group_by`, `group_wait`, `group_interval`, `repeat_interval`.
4. At minimum: what the alert means, likely causes, the first three
   diagnostic commands/queries, and an escalation path if unresolved in N
   minutes.
5. The Prometheus alerting rule fires first (decides something is wrong);
   Alertmanager's routing happens after, once the alert is received.

## Day 126 — REVIEW (Days 121–125: SRE Principles → Alertmanager)

**Cumulative quiz:**
1. SLI = measured signal; SLO = internal target for an SLI over a window;
   SLA = external/contractual promise, usually looser than the SLO, with a
   penalty for breach.
2. ≈216 minutes (~3.6 hours) for 99.5%/30 days.
3. Freeze risky releases and prioritize reliability work over features.
4. Toil = manual, repetitive, automatable work with no enduring value;
   SRE-book caps it at ~50% of an SRE's time.
5. A window shorter than ~4x the scrape interval doesn't have enough
   samples and produces noisy/misleading rates.
6. `histogram_quantile(0.99, sum(rate(x_bucket[5m])) by (le))`
7. A recording rule precomputes/stores a query result as a new time series
   (no alerting behavior); an alerting rule evaluates a condition and
   fires/routes an alert when true for `for:` duration.
8. USE = Utilization/Saturation/Errors, host/infra layer. RED =
   Rate/Errors/Duration, service/API layer.
9. Grouping bundles related firing alerts into one notification;
   inhibition suppresses a lower-severity alert while a related
   higher-severity one fires.
10. A `runbook_url` — so the on-call responder has a documented diagnosis/
    escalation path instead of guessing during an incident.

## Day 127 — Logging Stack I: EFK vs Loki, Deploy Loki+Promtail

**Warm-up:**
1. Metrics and traces.
2. A burn-rate alert measures how fast the error budget is being consumed
   relative to the SLO window; a plain threshold alert only fires on an
   absolute value crossing regardless of budget impact.
3. Grouping bundles related alerts into one notification; inhibition
   suppresses a lower-severity alert while a related higher-severity one fires.
4. So it's version-controlled, diffable, and reviewable rather than an
   untracked click-ops artifact.

**Recall:**
1. Loki indexes only labels (like Prometheus does for metrics), not full
   log content; Elasticsearch does full-text indexing of every log line,
   which is far more resource-hungry.
2. `request_id` is high-cardinality — as a label it would blow up Loki's
   index; it belongs in the log line content, queried via LogQL filters.
3. Promtail tails container logs, attaches Kubernetes labels
   (namespace/pod/container) automatically, and ships them to Loki; it
   runs as a DaemonSet (one pod per node).
4. `{app="project-app"} |= "timeout" != "retry"`
5. Logs answer "why"; traces answer "where" in a distributed call chain.

## Day 128 — Logging II: Parsing/Labels, LogQL, Retention, Structured Logging

**Warm-up:**
1. Loki is far cheaper to run (label-only indexing) and LogQL mirrors
   PromQL — the lighter operational/pedagogical choice vs Elasticsearch's
   resource-hungry full-text indexing.
2. High-cardinality labels like `request_id` would blow up Loki's index.
3. Kubernetes labels: namespace, pod, container.
4. `{app="project-app"} |= "error"`

**Recall:**
1. A log stream selector (`{app="x"}`) is label-based and cheap — it
   selects which streams to read; a line filter/parser (`|= "error"`,
   `| json`) operates on the log content within those streams.
2. `sum(count_over_time({app="project-app"} | json | status_code >= 500 [5m]))`
3. timestamp, level, message, and (once tracing is added) trace_id/span_id.
4. `limits_config.retention_period` (plus the compactor's
   `retention_enabled` flag).
5. Derived fields — turn a matched value (e.g. `trace_id`) in a log line
   into a clickable link to another tool (e.g. Jaeger).

## Day 129 — Tracing: OpenTelemetry, Instrument the Project, Jaeger, Correlation

**Warm-up:**
1. timestamp, level, message, trace_id/span_id (once tracing is added).
2. `sum(count_over_time({app="project-app"} | json | status_code >= 500 [5m]))`
3. `limits_config.retention_period`.
4. It turns a matched value in a log line (e.g. `trace_id`) into a
   clickable link to another data source/tool.

**Recall:**
1. A trace is a tree of spans; each span is one unit of work (name,
   start/end time, parent span ID, attributes) nested within the trace.
2. W3C Trace Context propagation via the `traceparent` header, carrying
   trace/span IDs across process/network boundaries.
3. Head-based sampling decides at trace start (e.g., 10% of all traces) —
   simple but may miss rare errors; tail-based sampling decides after
   seeing the whole trace (e.g., keep all errors) — more useful but more
   expensive/complex.
4. The `trace_id` (ideally `span_id` too).
5. Metric spike → identify the affected time window/service → query logs
   for that window → find a `trace_id` in a log line → jump to Jaeger →
   inspect the trace waterfall for the slow span.

## Day 130 — ★ Project Fully Instrumented: SLO Dashboards + Burn-Rate Alerts

**Warm-up:**
1. A trace is a tree of spans; each span is one unit of work with a
   parent-child relationship to other spans in the trace.
2. It must carry the `trace_id` (ideally `span_id` too).
3. Metrics (what), logs (why), traces (where).
4. verify: check `docs/slo.md` for the actual number — using the Day 121
   worked example of a 99.5%/30-day SLO, that's ≈216 minutes.

**Recall:**
1. A burn rate of 14.4x means the error rate is 14.4x the rate the SLO
   allows — consuming 2% of the 30-day budget per hour, i.e. the whole
   budget in ~2 days (30/14.4). It's the Google SRE workbook's standard
   fast-burn factor: page immediately. (Day file corrected 2026-07-05 —
   an earlier draft said "~2 hours".)
2. A short window alone gives false pages on brief blips; a long window
   alone detects real slow burns too late. Combining them confirms a
   genuine, sustained burn quickly without over-alerting.
3. Current 30-day availability vs target, error budget remaining (% and
   minutes), and 1h/6h burn-rate trend with reference lines.
4. A metric, a structured log (with trace context), and a trace.
5. It's cheap to implement once the pillars exist and reliably
   distinguishes real, budget-threatening degradation from noise — a flat
   threshold alert either pages too late on fast burns or too often on
   slow ones.

## Day 131 — Incident Management: Severity, Comms, Postmortems, Tabletop

**Warm-up:**
1. Consuming the error budget far faster than sustainable — the standard
   fast-burn factor used to trigger an immediate page.
2. A metric, a structured log (with trace_id), and a trace.
3. Short window catches fast real burns quickly without over-alerting;
   long window confirms slower sustained burns a short window alone would
   miss or falsely flag.
4. `d130-instrumented`

**Recall:**
1. The Incident Commander drives the response process (coordination,
   comms, decisions) but isn't necessarily fixing the issue; the debugger
   is the person actively diagnosing/mitigating.
2. So severity assessment is consistent and fast, mapped directly to
   pre-defined alert signals, rather than an ad hoc judgment call under
   pressure.
3. Summary, Timeline, Root cause, What went well/what didn't, Action items.
4. They measure whether the observability investment (Days 121–130) is
   actually reducing how long incidents take to notice and fix — trending
   shows real improvement or regression over time.
5. A tabletop is a verbal walkthrough/discussion with no live system
   interaction; a game day (Day 145) injects a real fault into the running
   system.

## Day 132 — GitOps I: Pull vs Push, ArgoCD Install, First App, App-of-Apps

**Warm-up:**
1. Summary, Timeline, Root cause, What went well/what didn't, Action items.
2. IC drives the incident process/coordination; the debugger actively
   diagnoses/fixes.
3. So severity stays consistent and mapped to alert signals rather than
   decided ad hoc under pressure.
4. A tabletop is verbal-only, no live system interaction; a live game day
   injects a real fault into the running system.

**Recall:**
1. Push has CI apply changes directly to the cluster (CI holds cluster
   credentials, drift can occur silently); pull has an in-cluster agent
   (ArgoCD) continuously reconcile cluster state to match git, so git
   stays the source of truth and drift is detected/corrected.
2. `Synced`/`OutOfSync` describes whether live cluster state matches the
   git-declared state; `Healthy`/`Degraded` describes whether the resulting
   workload is actually functioning correctly.
3. App-of-apps is a root Application pointing at a directory of other
   Application manifests; it scales because bootstrapping N apps becomes
   one create/sync instead of N manual `argocd app create` calls.
4. Under a manual sync policy, ArgoCD flags it `OutOfSync` but leaves the
   change until a human syncs; under automated sync with `selfHeal`,
   ArgoCD reverts the manual scale back to git's declared value
   automatically.
5. CI's job ends once it builds, tests, and updates the manifest/values
   file in git with the new version; ArgoCD's job begins there — actually
   reconciling the cluster to match.

## Day 133 — REVIEW (Days 127–132: Logging → GitOps Basics)

**Cumulative quiz:**
1. Loki is far cheaper to run (label-only indexing vs full-text) and its
   LogQL mirrors PromQL — the lighter operational/pedagogical choice.
2. `request_id` is high-cardinality; as a label it would blow up the
   index — keep it in the log line, query via filters.
3. `{app="project-app"} | json | status_code >= 500`
4. timestamp, level, message, trace_id/span_id.
5. A trace is a tree of spans; each span is one unit of work with its own
   timing and a parent-child relationship to other spans.
6. The `trace_id` (and span_id).
7. 14.4x means the error rate is consuming budget far faster than
   sustainable — a page-level fast-burn factor; short+long windows avoid
   both slow detection (long alone) and false pages on blips (short alone).
8. Summary, Timeline, Root cause, What went well/what didn't, Action items.
9. Push has CI apply changes directly to the cluster; pull (GitOps) has an
   in-cluster agent reconcile cluster state to match git continuously.
10. App-of-apps is a root Application pointing at a directory of child
    Application manifests; it solves bootstrapping/scaling many apps
    without N manual creations.

## Day 134 — GitOps II: ★ Project Managed by ArgoCD (Sync, Prune, Rollback)

**Warm-up:**
1. Push has CI apply directly to the cluster; pull has an in-cluster agent
   (ArgoCD) reconcile cluster state to match git continuously.
2. `OutOfSync` means live state doesn't match git's declared state; a sync
   (manual or automated/self-heal) corrects it.
3. It bootstraps/scales many child Applications from one root Application
   instead of creating each manually.
4. `d130-instrumented`

**Recall:**
1. `prune: true` deletes cluster resources whose manifests were removed
   from git; `selfHeal: true` automatically reverts manual cluster drift
   back to match git.
2. The GitOps-native rollback is `git revert` on the bad commit, letting
   ArgoCD reconcile automatically; `argocd app rollback <app> <revision>`
   is a faster convenience achieving the same end state without a manual
   revert commit, but git should still get a revert committed afterward.
3. The image updater watches a registry for new tags and auto-commits/
   patches the manifest to deploy them; the main tradeoff is it writes to
   git automatically, blurring who/what changed production — often gated
   to non-prod or made reviewable.
4. Because a fast `argocd app rollback` doesn't update git — a revert
   commit keeps git history truthful about what's actually running.
5. Every project resource's desired state lives in git, automated sync
   with prune+selfHeal is active, a rollback was demonstrated end-to-end,
   and there's a documented path from new image tag to deployed state.

## Day 135 — Service Mesh I: Istio Concepts — Sidecars, VirtualServices, Traffic Splitting

**Warm-up:**
1. `prune` deletes resources removed from git; `selfHeal` reverts manual
   cluster drift back to match git automatically.
2. `git revert` the bad commit and let ArgoCD reconcile (or a fast
   `argocd app rollback` followed by a git revert).
3. It watches a registry for new image tags and automatically updates the
   deployed manifest.
4. `d134-gitops`

**Recall:**
1. The sidecar proxy (Envoy, injected into every pod) and the control
   plane (`istiod`, which configures every sidecar's rules).
2. A DestinationRule defines subsets of a service (e.g. by version label);
   a VirtualService defines routing rules across those subsets (weights,
   path routing, retries/timeouts).
3. Istio's weighted routing splits traffic by percentage independent of
   pod-replica counts; a plain Kubernetes Service load-balances by pod
   count only, which is coarse and hard to tune precisely.
4. By labeling the namespace: `kubectl label namespace <ns>
   istio-injection=enabled`.
5. Gateway.

## Day 136 — Istio II: mTLS, Canary Release, Mesh Observability (Kiali)

**Warm-up:**
1. A DestinationRule defines subsets; a VirtualService defines routing
   rules across those subsets.
2. By labeling the namespace `istio-injection=enabled`.
3. Because Istio splits traffic by weighted percentage regardless of pod
   replica counts, unlike relying on pod-count ratios.
4. That traffic split roughly proportionally (e.g., ~80/20) across two
   subsets purely via VirtualService weights, with zero app code changes.

**Recall:**
1. `PERMISSIVE` accepts both plaintext and mTLS traffic (safe migration
   default); `STRICT` accepts mTLS only and refuses plaintext.
2. Because the SLO/error-rate/latency instrumentation from Days 121–130
   gives an objective, real-time signal to judge whether the new version
   is healthy, making it safe to ramp or abort based on data rather than
   guessing.
3. Istio's canary decouples traffic percentage from pod/replica count
   entirely (precise weighted splits); a Kubernetes rolling update shifts
   traffic by gradually replacing pods (coupled to replica count).
4. Kiali shows the live service-to-service topology/traffic graph (who
   talks to whom, per-edge rate/error/latency, mTLS status) — a topology
   view Grafana's per-service dashboards don't provide.
5. Fault injection deliberately injects synthetic delays or error aborts
   to test whether timeout/retry logic degrades gracefully; enabled via
   `VirtualService.http.fault`.

## Day 137 — Chaos Engineering: Principles, Blast Radius, Experiments vs SLOs

**Warm-up:**
1. `PERMISSIVE` accepts both plaintext and mTLS; `STRICT` only accepts mTLS.
2. Istio's canary splits traffic by weight independent of pod count; a
   rolling update shifts traffic by replacing pods gradually.
3. The live service-to-service traffic graph/topology (rate, error,
   latency, mTLS status per edge).
4. `VirtualService.http.fault`

**Recall:**
1. Define steady state → hypothesize it holds under a specific failure →
   inject the failure → measure whether the hypothesis held → fix the gap
   if it didn't.
2. To limit potential damage/user impact while learning — confidence is
   built incrementally, expanding scope only after initial experiments
   succeed.
3. The SLO/error budget — whether it stayed within tolerance during the
   experiment — not a vague "did it seem okay" judgment.
4. Latency or packet loss between a service and a downstream dependency
   (e.g., a slow network link or a struggling dependency).
5. Chaos experiments need real SLIs/error-budget data to objectively judge
   pass/fail — without Day 130's instrumentation there's no reliable
   signal to measure against.

## Day 138 — Performance & Load: k6, Saturation Point, Capacity Math

**Warm-up:**
1. Define steady state → hypothesize it holds under a failure → inject the
   failure → measure → fix the gap.
2. To limit the blast radius/damage while confidence is still being built.
3. The SLO/error budget — whether it stayed within tolerance during the
   experiment.
4. Simulated network delay/loss between services; it should trigger the
   `HighLatency` alert (Day 123).

**Recall:**
1. A load test uses expected normal traffic to confirm SLOs hold; a stress
   test ramps well past expected traffic to find the breaking point.
2. A soak test sustains moderate load for a long duration, surfacing
   slow-building problems (memory leaks, gradual resource exhaustion) that
   short tests don't have time to reveal.
3. `thresholds: { http_req_duration: ['p(95)<500'] }`
4. Saturation point is the load level where latency/error rate starts
   degrading nonlinearly — identified visually as the "knee" of the curve.
5. Headroom ratio = 200 / 80 = 2.5x.

## Day 139 — Advanced Cluster Ops: Autoscaling, PDBs, Cost+Reliability

**Warm-up:**
1. A load test uses expected/normal traffic; a stress test ramps well past
   expected traffic to find the breaking point.
2. It surfaces slow-building problems (memory leaks, gradual resource
   exhaustion) that short load/stress tests don't run long enough to reveal.
3. verify: check `docs/capacity.md` from Day 138 for the project's actual
   saturation point and headroom numbers.
4. `thresholds: { http_req_failed: ['rate<0.01'] }`

**Recall:**
1. cluster-autoscaler works at the node-group level, reactively scaling
   ASGs when pods are unschedulable; Karpenter provisions right-sized
   nodes directly on demand (bin-packing aware, mixed instance
   types/Spot) without pre-defined node groups — faster scale-up, tighter
   bin-packing.
2. A PDB protects against voluntary disruption (drains, autoscaler
   scale-down, rolling updates) by limiting how many pods can be down at
   once; it does not protect against involuntary disruption (e.g. a node
   crashing outright).
3. It's a feature — it enforces the availability floor the team declared;
   if it blocks a drain, the replica count/PDB combination needs resizing
   together, not the PDB removed.
4. The SLO/error budget, the saturation point (load testing), and cost
   (FinOps).
5. Karpenter provisions right-sized nodes directly and faster, with better
   bin-packing and instance-type/Spot flexibility, without needing
   pre-defined node groups — generally lower cost per unit of capacity and
   quicker scale-up than cluster-autoscaler.

## Day 140 — REVIEW (Days 134–139: GitOps Milestone → Autoscaling/PDBs)

**Cumulative quiz:**
1. `prune` deletes resources removed from git; `selfHeal` reverts manual
   drift back to match git.
2. `git revert` the bad commit (ArgoCD reconciles automatically), or a
   fast `argocd app rollback` followed by a git revert to keep history honest.
3. `PERMISSIVE` accepts both plaintext and mTLS; `STRICT` accepts mTLS only.
4. Istio splits traffic by weighted percentage independent of pod-replica
   count; a rolling update shifts traffic by gradually replacing pods.
5. Define steady state → hypothesize it holds under a failure → inject the
   failure → measure → fix the gap.
6. The SLO/error budget — whether it stayed within tolerance during the
   experiment.
7. A load test uses expected traffic to confirm SLOs hold; a stress test
   ramps well past expected traffic to find the breaking point.
8. The load level where latency/error rate starts degrading nonlinearly —
   the "knee" of the curve on the dashboard.
9. cluster-autoscaler scales pre-defined node groups reactively based on
   pending pods; Karpenter directly provisions right-sized nodes on demand
   without pre-defined groups, with better bin-packing/flexibility.
10. A PDB protects against voluntary disruption (drains, scale-downs,
    rolling updates); it does not protect against involuntary disruption
    (e.g. a node crashing).

## Day 141 — Platform Engineering: IDP Concept, Golden Paths, Backstage Install

**Warm-up:**
1. `git revert` the bad commit and let ArgoCD reconcile (or a fast
   `argocd app rollback` followed by a git revert).
2. Define steady state → hypothesize it holds under a failure → inject the
   failure → measure → fix the gap.
3. A load test uses expected traffic to confirm SLOs hold; a stress test
   ramps well past expected traffic to find the breaking point.
4. Voluntary disruption — it limits how many pods of a set can be down at
   once during drains/scale-downs/rolling updates.

**Recall:**
1. Catalog (every service/owner/dependency/links), Software templates
   (scaffold new services), TechDocs (docs-as-code rendered in-portal).
2. A golden path is the paved, supported, opinionated-default way to do
   something common; it reduces cognitive load/toil while still allowing
   deviation for real reasons.
3. `catalog-info.yaml` — `kind: Component`, metadata (name, owner),
   `spec.type`, `spec.lifecycle`, and links to dashboards/runbooks/etc.
4. Platform engineering packages the platform team's accumulated expertise
   into self-service so other developers don't repeat the toil of learning
   every tool themselves — directly reducing toil org-wide.
5. It adds a unified front door/portal linking to all of them per service
   (dashboard, ArgoCD app, runbooks, owner) — it doesn't replace any
   underlying tool.

## Day 142 — Backstage II: Software Templates, Scaffolding, TechDocs

**Warm-up:**
1. Software catalog, software templates, TechDocs.
2. The paved, supported, opinionated-default way to do something common,
   reducing cognitive load/toil while allowing deviation.
3. `catalog-info.yaml`.
4. It adds a unified front door linking to all of them per service rather
   than replacing them.

**Recall:**
1. Parameters (input form), steps (actions run — fetch/publish/register),
   skeleton (the templated files that get rendered).
2. Nunjucks-style placeholders, e.g. `${{ values.name }}`.
3. An `mkdocs.yml` referencing the repo's `docs/` folder, plus a
   `backstage.io/techdocs-ref` annotation in `catalog-info.yaml`.
4. It turns what took days to build by hand into a 5-minute self-service
   action for the next engineer — the concrete, measurable payoff of
   platform engineering.
5. Making golden paths so rigid that any legitimate deviation requires
   fighting the platform.

## Day 143 — DevSecOps: SAST/Dep Scanning, SBOM, OPA/Gatekeeper Policy

**Warm-up:**
1. A pre-wired directory structure, a starter CI pipeline, and a
   `catalog-info.yaml` (plus optionally a Helm chart skeleton/ArgoCD
   Application).
2. Nunjucks-style placeholders, e.g. `${{ values.name }}`.
3. An `mkdocs.yml` referencing `docs/` plus the `backstage.io/techdocs-ref`
   annotation.
4. Templates so rigid that legitimate deviation requires fighting the
   platform.

**Recall:**
1. SAST scans source code statically for known bad patterns (hardcoded
   secrets, injection risks) without running it; SCA/dependency scanning
   checks third-party packages/images against known-CVE databases.
2. A blanket zero-findings gate usually just gets bypassed under deadline
   pressure — a severity-based gate (fail CRITICAL/HIGH, warn MEDIUM/LOW)
   is more sustainable.
3. An SBOM is a machine-readable manifest of every component (direct +
   transitive) in a build; it lets you answer "are we affected?" quickly
   when a new CVE drops, by searching manifests instead of re-scanning.
4. A `ConstraintTemplate` defines the Rego policy rule; a `Constraint`
   specifies where/how that rule is applied (e.g., scoped to a namespace).
5. CI only catches what goes through the pipeline — someone can still
   `kubectl apply` an unscanned manifest directly; admission-time policy
   closes that gap by rejecting non-compliant manifests at the cluster
   boundary.

## Day 144 — Reliability Patterns: Timeouts, Retries, Circuit Breakers

**Warm-up:**
1. SAST scans source code statically without running it; dependency/SCA
   scanning checks third-party packages/images against known CVEs.
2. To answer "are we affected?" quickly when a new CVE is disclosed, via
   the manifest instead of re-scanning from scratch.
3. A `ConstraintTemplate` is the Rego policy rule; a `Constraint` specifies
   where/how it's applied.
4. CI only catches what goes through the pipeline; admission-time policy
   rejects non-compliant manifests applied directly to the cluster.

**Recall:**
1. A timeout longer than the calling path's latency SLO provides no real
   protection — the call could exceed the SLO's own budget before timing
   out, so the timeout must be shorter to actually bound the impact.
2. Naive immediate retry can synchronize and amplify load on an already-
   struggling dependency (a "retry storm"); exponential backoff with
   jitter spreads retries out and avoids thundering-herd synchronization
   across clients.
3. Closed (normal, calls flow), Open (failing fast, no calls, cooldown
   running), Half-open (a trial call after cooldown decides whether to
   close again or reopen).
4. Liveness checks if the process is alive (restart if not); readiness
   checks if it can serve traffic right now (remove from load balancing if
   not) — a readiness check that transitively pings a struggling
   downstream dependency can cause a cascading failure by pulling healthy
   pods out of rotation because one dependency is degraded.
5. A "related items"/decorative stat sub-call that, if it fails or times
   out, is omitted from the response instead of failing the whole request
   (main response still returns 200 with reduced content).

## Day 145 — Game Day: Live Incident Simulation (Facilitator Session)

**Warm-up:**
1. Closed, Open, Half-open.
2. A timeout longer than the SLO's own latency target provides no real
   bound on user-facing impact.
3. Liveness checks if the process is alive (restart if not); readiness
   checks if it can serve traffic right now (pull from load balancing if
   not).
4. Dashboards (Day 124/130), alerts (Day 123/125/130), logs (Day 127/128),
   traces (Day 129), and ArgoCD (Day 132/134).

**Recall:**
1. Time-to-mitigate is how long until the service is healthy again;
   time-to-root-cause is how long until the underlying cause is
   understood — mitigation can happen well before root cause is known.
2. So the responder practices independent diagnosis rather than defaulting
   to owner-led debugging — the point of the exercise is testing the
   on-call muscle itself.
3. Because incident duration only matters relative to how much of the
   30-day error budget it burned — a short incident on a tight budget can
   matter more than a longer one on a healthy budget.
4. A good action item has a specific owner, a due date, and addresses a
   real, specific gap (e.g., a missing runbook step) rather than being
   vague or blame-adjacent.

## Day 146 — SRE Interview Prep: Drills, "Design Monitoring for X", SLO Math

**Warm-up:**
1. Time-to-mitigate is how long until the service is healthy again;
   time-to-root-cause is how long until the underlying cause is understood.
2. Specific, owned, and dated action items tied to real, concrete gaps
   (versus vague/generic ones).
3. verify: depends on what the actual Day 145 session revealed — expect
   the trainee to name whatever tooling/runbook gap surfaced live.

Note: Block 1 (troubleshooting drills) and Block 2 ("design monitoring for
X") already carry their expected-answer guidance inline in the day file
(the `(expects: ...)` notes) — not duplicated here.

**Block 3 — SLO math drill answers** (not given inline in the day file):
1. 99.9% SLO over 30 days → error budget ≈ 43.2 minutes (0.1% × 43,200 min).
2. Burn rate 6x on a 30-day window → budget exhausted in 30 / 6 = 5 days.
3. 60% of budget consumed with 10 of 30 days left means 66.7% of the
   window has elapsed against 60% of budget used — a slightly better-than-
   linear pace, leaving a thin cushion (40% budget for the final 33% of
   the window). Per the Day 121 error-budget policy (spent budget → freeze
   risky releases), the prudent call is to lean cautious: hold, canary, or
   flag-gate the risky feature rather than ship it outright, since one bad
   burn event late in the window could exhaust the remainder. ⚠ (judgment
   call, not a single "correct" number)
4. 99.95% availability → downtime/year ≈ 0.05% × 525,600 min/yr ≈ 262.8
   minutes ≈ 4.38 hours/year (~4h 23m).
5. Two independent 99.9% services in series: compound availability =
   0.999 × 0.999 = 0.998001 ≈ 99.8% — lower than either individual
   service, since both must be up simultaneously.

**Recall (end-of-day questions):**
1. Naming the SLI first grounds the design in what actually matters to the
   user, avoiding an answer that lists tools without a clear measurement
   target — SLIs first, tools second, is what separates senior-level
   answers from tutorial-level ones.
2. A batch job has no request rate — its correctness is about whether it
   completed, how long it took, and whether the output is fresh/correct,
   so its SLI shape is completion + duration + freshness, not RED-style
   request metrics.
3. 30 / 6 ≈ 5 days.
4. Lower — 0.999 × 0.999 = 0.998001 ≈ 99.8%, because both services must be
   up simultaneously for the request to succeed.

## Day 147 — REVIEW (Days 141–146: Platform Engineering → Interview Prep)

**Cumulative quiz:**
1. Catalog, software templates, TechDocs.
2. A golden path is the paved, supported, opinionated-default way to do
   something common; it solves inconsistency and repeated toil across teams.
3. SAST scans source code statically without running it; SCA/dependency
   scanning checks third-party packages/images against known CVEs.
4. An SBOM is a machine-readable manifest of every component in a build,
   used to quickly determine exposure when a new CVE is disclosed.
5. A `ConstraintTemplate` defines the Rego rule; a `Constraint` specifies
   where/how it's applied.
6. A timeout longer than the SLO's latency target provides no real bound
   on user-facing impact — it must be shorter to actually protect it.
7. Closed, Open, Half-open.
8. Time-to-mitigate is how long until the service is healthy again;
   time-to-root-cause is how long until the underlying cause is actually
   understood.
9. Naming the SLI first grounds the design in what matters to the user
   before jumping to tool names — separates senior answers from
   tutorial-level ones.
10. 30 / 6 = 5 days.

## Day 148 — Resume Checkpoint #2 + Portfolio Polish (Facilitator Session)

**Warm-up:**
1. Time-to-mitigate is how long until the service is healthy again;
   time-to-root-cause is how long until the underlying cause is
   actually understood.
2. verify: check the actual Day 145 postmortem action-item log for what it
   led to (e.g. a runbook update or an alert-threshold change).
3. verify: depends on the trainee's actual project state since Day 111 —
   expect the observability stack, GitOps management, Istio canary, or
   chaos-testing capability as candidates.

**Recall:**
1. Action verb + what + how + measurable result.
2. So nothing on the resume is unverifiable by a reviewer clicking through
   to the actual GitHub repo.
3. The app, the service mesh (Istio), the GitOps flow (ArgoCD), the
   observability stack (Prometheus/Grafana/Loki/Jaeger), and the AWS
   deployment target.
4. What the project does, the architecture diagram, a "how to run it"
   section, and links to the SLO doc/runbooks/dashboards.

## Day 149 — Mock Interviews: DevOps + SRE Loops, Feedback, Gap List

**Warm-up:**
1. Action verb + what + how + measurable result.
2. The app, the mesh (Istio), the GitOps flow (ArgoCD), the observability
   stack, and the AWS deployment target.
3. verify: depends on the trainee's actual resume draft from Day 148.

**Recall:**
1. Process (dashboard → logs → traces → hypothesis → test, in order) was
   scored more heavily than reaching the exact root cause within the time box.
2. verify: depends on the trainee's actual mock-interview gap list from
   this session.
3. Because it draws on a real, lived incident (Day 145's game day) rather
   than a hypothetical, testing whether the trainee can narrate a genuine
   anecdote with an outcome.
4. Be direct, reduce load, and don't sugarcoat (interaction-guide norms
   referenced from Day 111/121).

## Day 150 — ★ Phase 5 Capstone: GitOps-Managed, Meshed, SLO'd, Chaos-Tested Platform

**Warm-up:**
1. verify: depends on the trainee's actual Day 149 gap list.
2. Process (dashboard→logs→traces→hypothesis→test order) was scored more
   heavily than reaching the exact root cause.
3. verify: depends on which scenario the owner picked from Day 145's menu
   (bad deploy / resource exhaustion / dependency slowness) and what the
   resulting postmortem surfaced.
4. `d134-gitops`

**Recall:**
1. Phase 3's milestone was the project simply running on Kubernetes
   (deployed); Phase 5's milestone is the project being operated — with
   SLOs, self-healing GitOps delivery, safe canary releases, verified
   chaos tolerance, a self-service catalog front door, and codified
   incident response.
2. Any three of: SLOs defined and documented; metrics+logs+traces
   correlating; burn-rate alerts firing correctly; ArgoCD managing all
   resources with self-heal; a canary release with an abort path; a
   documented chaos-experiment finding; load-test/capacity headroom
   documented; PDB/autoscaling posture documented; Backstage cataloging
   with working links; CI SAST/SCA/SBOM gates + two Gatekeeper policies;
   reliability patterns verified under fault.
3. Because a fresher who can name their system's real gaps reads as more
   senior than one who claims everything is perfect — and it keeps the
   write-up honest and verifiable.
4. Anomaly detection on the Prometheus metrics built in Days 122–130
   (Phase 6's Day 165 work depends directly on this instrumentation).
