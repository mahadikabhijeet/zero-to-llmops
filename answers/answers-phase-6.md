# Answer Key — Phase 6 (Days 151–180)

Trainer prep companion. Answers are grounded in each day's theory section; ⚠ marks
answers drawn from general knowledge beyond the day file — verify against official
docs before teaching.

---

## Day 151 — ML for Ops Engineers: Lifecycle, Train/Serve Split, scikit-learn Hello-Model

**Warm-up:**
1. Checkpoint #1 (D111, Phase 4 capstone) covered cloud deployment basics (e.g. "deployed to EC2"). Checkpoint #2 (D148) adds everything from Days 91–147 — SLOs, the Prometheus/Grafana/Loki/Jaeger stack, GitOps via ArgoCD, Istio canary releases, chaos engineering, k6 load testing, Backstage platform work, DevSecOps scanning/policy, reliability patterns, and the Day 145 live incident — rewritten as outcome-based bullets that supersede the vaguer Phase 4 claims.
2. "GitOps-managed" means ArgoCD manages all of the project's cluster resources with self-heal active — no manual `kubectl apply`; drift is auto-corrected back to what's in git.
3. GitOps-managed, meshed (Istio), SLO'd, chaos-tested.
4. ⚠ An error budget is 100% minus the SLO target (e.g. a 99.9% SLO leaves a 0.1% budget) — the allowed amount of unreliability over a window. It matters for release decisions because burning through it quickly should slow or halt risky releases, while remaining budget supports shipping faster.

**Recall:**
1. Five stages, mapped to DevOps: data → train → evaluate → serve → monitor → retrain, analogous to source data/a build/testing/deployment/observability-alerting/a CI re-run.
2. Never evaluate on training data because the model can simply memorize it, giving an inflated accuracy that says nothing about real-world generalization — the ML equivalent of "never test in prod."
3. `.fit(X_train, y_train)` trains the model; `.predict(X_test)` runs inference and returns predictions for new inputs.
4. Training is offline/batch and can tolerate being slow; serving is online and latency-sensitive, answering requests in real time.
5. `random_state` fixes the pseudo-random seed for reproducible splits/training — pinning it is the ML equivalent of pinning a dependency version so results are stable across reruns.

---

## Day 152 — MLflow: Tracking, Registry, and the Project's First Real Model

**Warm-up:**
1. Five stages, in order: data → train → evaluate → serve → monitor → retrain.
2. Training tolerates slow, offline batch execution; serving must be fast and online.
3. `.fit()` trained the RandomForestClassifier on the training split; `.predict()` produced class predictions on the test split.
4. `random_state=42` was set for reproducible splitting and training.

**Recall:**
1. A "run" is one execution of a training script; an "experiment" is a named group of related runs (e.g. "anomaly-detector").
2. `mlflow.log_*` captures params (hyperparameters), metrics (e.g. accuracy), and artifacts (model files/outputs) per run.
3. A registry stage (Staging/Production/Archived) marks a registered model version's promotion state — analogous to promoting a container image/Helm chart from dev to staging to prod.
4. `mlruns/` is gitignored because it's a local, regenerable tracking store (build output); the training script is committed because it's the actual source of truth that produces the runs.
5. Real Prometheus data was pulled now (not waiting for Day 165) so the eventual anomaly detector's data pipeline is grounded in real historical metrics from the start, and to exercise the pull/flatten pattern early.

---

## Day 153 — DVC: Data Versioning, Pipelines, Remotes, Reproducibility

**Warm-up:**
1. A run is one training execution; an experiment groups related runs.
2. Promoting to "Staging" moves a specific registered model version into that named stage, marking it for pre-production use/testing.
3. Yesterday's dataset came from the project's real Prometheus metrics, pulled via PromQL/the HTTP API into a CSV.
4. `mlruns/` was gitignored because it's a local, regenerable tracking store, not a source of truth.

**Recall:**
1. A `.dvc` pointer file holds a hash and size of the real data file; it's small because the actual data lives in DVC's cache/remote, not in git.
2. `dvc add` manually versions raw/source data; a `dvc.yaml` pipeline output is produced and owned automatically by a pipeline stage — you shouldn't hand-`dvc add` a stage's output.
3. `dvc repro` re-runs a stage when the hash of any declared dependency (code, data, or params) has changed; unchanged deps mean the stage is skipped.
4. `dvc push`/`dvc pull` move the actual cached data (not pointers) to/from a configured remote (local dir, S3, GDrive, etc.); git only ever stores the pointer.
5. A local directory needs zero extra infra and proves the same workflow; moving to S3 later is only a `dvc remote add` config change, no code change.

---

## Day 154 — REVIEW (Days 148–153)

**Cumulative quiz:**
1. Five ML lifecycle stages/analogues: data→train→evaluate→serve→monitor→retrain ≈ source data/build/test/deploy/observability/CI re-run.
2. A model must never be evaluated on its own training data because it can memorize it, giving an inflated, non-generalizing accuracy — same as "never test in prod."
3. A run is one training execution; an experiment groups related runs.
4. Promoting to registry "Staging" moves that model version into the Staging stage; the container/Helm analogy is promoting an image/chart from a dev to a staging environment tag.
5. The real dataset came from the project's Prometheus metrics, pulled via PromQL/HTTP API.
6. DVC solves data versioning — MLflow tracks runs/params/metrics/model artifacts but not the raw data files feeding a run; a silently-changed CSV would break reproducibility despite MLflow "looking" fine.
7. A `.dvc` pointer file contains a hash + size of the real file; it's small because it never stores the data itself.
8. `dvc repro` re-runs a stage when the hash of any of its declared dependencies has changed.
9. `dvc add` is a manual, one-off versioning action for raw/source data; a `dvc.yaml` stage owns and generates its declared outputs automatically — never hand-`dvc add` a pipeline output.
10. GitOps-managed, meshed, SLO'd, chaos-tested.

---

## Day 155 — Model Serving: FastAPI Wrapper, Containerize, Deploy via Existing Helm/CI

**Warm-up:**
1. `mlflow.pyfunc.load_model("models:/<name>/<stage>")` (e.g. `models:/anomaly-detector-poc/Staging`).
2. `dvc repro`.
3. A `dvc.lock` file holds the resolved dependency graph/hashes for each pipeline stage's deps, outs, and params — DVC's equivalent of a dependency lockfile.
4. Training is slow/offline/batch and serving must be fast/online — mixing them risks blocking real-time requests and couples serving to the training environment being up.

**Recall:**
1. Loading once at startup avoids the classic ML-serving latency bug of a slow per-request model load.
2. `/health` is a liveness-style probe confirming the process is alive; `/v1/predict` is the actual inference/work endpoint — same split as Day 69's liveness/readiness discipline.
3. Baking the model into the image is simplest and most reproducible for now, with no runtime dependency on the registry being reachable; the tradeoff vs pulling at start is losing fast model-swap iteration for that reproducibility.
4. Build → scan (trivy) → push to registry → Helm upgrade — identical shape to the Phase 2/3 app pipeline.
5. Versioning the API path prevents a future model/response-shape change from silently breaking existing callers.

---

## Day 156 — Kubeflow / Managed-MLOps Overview: Platform vs DIY, SageMaker Glance

**Warm-up:**
1. Loading once at startup avoids per-request load latency.
2. `/health` (liveness) and `/v1/predict` (inference).
3. Build, scan, push, and Helm deploy — reused from the Phase 2/3 app pipeline.
4. Baking the model in trades fast iteration for simplicity/reproducibility; pulling it at start trades a new runtime dependency on the registry for faster model swaps.

**Recall:**
1. A Kubeflow Pipeline is a DAG of containerized steps (data prep → train → evaluate → deploy); each "component" is just a container, orchestrated like Argo Workflows under the hood.
2. KServe adds autoscaling-to-zero and canary/traffic-split rollouts for models on top of a bare FastAPI+Helm deployment.
3. SageMaker manages: notebooks, training jobs (on-demand GPU instances billed per job), and endpoints/serving (with built-in autoscaling and A/B/canary traffic splitting) — plus a managed Model Registry.
4. Signals: many models/teams sharing infra, need for managed GPU scheduling, need for a built-in experiment UI/governance, and compliance/audit-trail requirements.
5. DIY is right because the fresher project is a single model, small team, on infra already operated (k8s cluster + registry + CI) — a platform would add ops burden without removing enough toil to justify it.

---

## Day 157 — ML CI/CD: Retraining Pipeline, Model Gates, Promotion Flow

**Warm-up:**
1. `/health` (liveness) and `/v1/predict` (inference/serving).
2. One DIY→Kubeflow mapping: FastAPI+Helm serving → KServe. One DIY→SageMaker mapping: MLflow registry → SageMaker Model Registry.
3. Many models/teams sharing infra, managed GPU scheduling needs, a built-in experiment UI/governance, or compliance/audit requirements.
4. `dvc repro` reported "nothing to reproduce" and skipped all stages.

**Recall:**
1. Reproduce (dvc repro pulls data + retrains) → gate (automated accuracy/regression/sanity checks) → register (add version to MLflow registry) → promote (auto to Staging, human-approved to Production).
2. Schema test (input/output shape/type matches the contract), invariance test (an irrelevant small perturbation shouldn't change the prediction), regression test (fixed "golden" inputs with known expected outputs, catching silent behavior drift).
3. Because promoting to Production is a hard-to-reverse, high-impact action — the same "a human confirms hard-to-reverse actions" principle applied everywhere else.
4. Rollback = re-point serving at the previous Production-tagged registry version — no retraining needed, since the registry retains every version (same instant-rollback property as a Helm/ArgoCD rollback).
5. `gate.py` checks (a) accuracy against a fixed floor and (b) accuracy against the currently-registered Production model's logged accuracy (no regression).

---

## Day 158 — Model Monitoring: Drift, Performance Tracking, Feedback Loops

**Warm-up:**
1. Reproduce, gate, register, promote.
2. Schema test, invariance test, regression test — protecting against shape/type mismatches, sensitivity to irrelevant noise, and silent behavior drift respectively.
3. Because it's a hard-to-reverse, high-stakes action requiring human judgment.
4. Instant rollback to any previous registered model version, without retraining.

**Recall:**
1. Data drift = input distribution shifts from what the model trained on; concept drift = the input→output relationship itself changes even if inputs look the same; prediction drift = the distribution of the model's outputs shifts (a cheap, label-free proxy for the other two).
2. A degraded model still returns confident HTTP 200 responses with wrong answers — there is no exception/crash to alert on, unlike a crashed pod.
3. A Counter (`predictions_total`, labeled by class), a Histogram (`prediction_latency_seconds`), and a Gauge for the rolling mean of a key input feature (a drift proxy).
4. It can reinforce the model's own (possibly wrong) predictions, entrenching errors, if it never sees real ground truth.
5. Via a feedback path such as an Alertmanager webhook triggering a GitHub Actions `repository_dispatch` that kicks off the `ml-retrain.yml` workflow.

---

## Day 159 — LLM Fundamentals for Ops: Tokens, Inference, GPU Basics, Cost Levers

**Warm-up:**
1. Data drift is a shift in input distribution; concept drift is a change in the input→output relationship itself.
2. Because it fails "silently" — confident wrong answers with no crash/exception to alert on.
3. A rolling mean of a key input feature, as a drift proxy.
4. An accuracy-floor check (or a no-regression comparison against the current Production model).

**Recall:**
1. A token is a sub-word unit of text, not a whole word — words can span multiple tokens (or vice versa); roughly ~4 characters/token in English is the rule of thumb.
2. Exceeding the context window causes truncation or a hard error — a real operational limit, like a request body size limit.
3. Output length dominates latency because tokens are generated sequentially, one at a time; more output tokens means more sequential generation steps.
4. Caching, batching, and quantization.
5. Training an LLM from scratch needs massive GPU clusters far beyond the scope of the course or a first job — the ops role is to serve, wrap, and operate a model someone else trained.

---

## Day 160 — LLMOps I: Serving a Small OSS Model (CPU-Friendly), OpenAI-Compatible API

**Warm-up:**
1. A token is a sub-word unit of text, roughly ~4 characters in English.
2. Truncation or a hard error.
3. Because output tokens are generated sequentially, one at a time.
4. Two of: caching, batching, quantization.

**Recall:**
1. Because vLLM is built around GPU batching and is not a good CPU fit; Ollama (backed by llama.cpp) runs small quantized models acceptably on CPU-only commodity hardware.
2. It means the server exposes an endpoint shaped like OpenAI's chat completion API (`/v1/chat/completions`, same request/response shape) — client code, load-testing scripts, and observability wrappers don't need to change when the underlying runtime is swapped.
3. Quantization reduces the numeric precision of model weights (e.g. 16-bit → 4-bit) to shrink memory footprint and speed CPU inference, at some quality cost.
4. The model/daemon has to load into memory on the first request (or after being evicted) — same phenomenon as a serverless cold start or pod startup.
5. ⚠ The trainee's own measured values: model name, quantization level (from `ollama show`), and average steady-state latency over 10 sequential requests — these are lab-specific measurements, not fixed numbers in the file.

---

## Day 161 — REVIEW (Days 155–160)

**Cumulative quiz:**
1. It avoids the per-request model-load latency bug.
2. `/health` (liveness) checks the process is alive; `/v1/predict` (inference) does the real work.
3. One DIY→managed mapping: MLflow registry → SageMaker Model Registry. One DIY-favoring signal: a single model/small team on infra already operated.
4. Reproduce → gate → register → promote.
5. Because it's a hard-to-reverse action requiring human judgment.
6. Data drift = input distribution shift; concept drift = the input-output relationship itself changes.
7. Because it returns confident-looking wrong answers with no exception to alert on.
8. A token is a sub-word text unit; exceeding the context window causes truncation or a hard error.
9. Because output tokens generate sequentially, one at a time, so more output means more sequential latency-driving steps.
10. Because vLLM assumes GPU batching and isn't CPU-friendly, while Ollama/llama.cpp runs quantized models acceptably on CPU commodity hardware.

---

## Day 162 — LLMOps II: RAG Architecture — Embeddings, Vector DB, Minimal RAG over Project Docs

**Warm-up:**
1. The server exposes an endpoint shaped like OpenAI's API so client code is portable across runtimes.
2. Ollama (llama.cpp-backed), because it runs acceptably on CPU unlike GPU-oriented vLLM.
3. The model/daemon loading into memory on the first request (or after eviction).
4. It reduces weight precision (e.g. 16-bit → 4-bit) to shrink memory footprint and speed inference, at some quality cost.

**Recall:**
1. RAG solves the problem that an LLM only knows its training data plus whatever's currently in its context window — it has no live access to private/current documents unless that text is retrieved and injected into the prompt.
2. The same embedding model must be used for indexing and querying so the query vector lands in the same vector space as the indexed chunks — otherwise similarity comparisons are meaningless.
3. Documents are chunked because embedding models have their own context limits, and retrieval precision improves with smaller, topic-coherent passages rather than whole files.
4. User asks a question → embed the question → query the vector DB for top-K similar chunks → build a prompt (instructions + chunks + question) → send to the LLM → return the grounded (ideally cited) answer.
5. Add an explicit prompt instruction to answer only from the provided context and say "not in the docs" if unsure.

---

## Day 163 — LLMOps III: Agents, Tool Use, Guardrails, Eval Basics

**Warm-up:**
1. RAG grounds an LLM's answers in retrieved private/current documents, which a plain call can't access.
2. Because the query embedding must land in the same vector space as the indexed chunks for similarity search to be meaningful.
3. Ask question → embed question → query vector DB for top-K chunks → build prompt with chunks + question → send to LLM → return grounded answer.
4. Add an explicit instruction to only answer from context and say "not in the docs" if unsure.

**Recall:**
1. The LLM only proposes which tool to call and with what arguments; your code is what actually executes the function/action — the model never runs anything itself.
2. Read-only tools can't change state or cause damage, so they're safe to auto-execute; state-changing/destructive tools risk irreversible harm and need a human confirmation step, echoing the "confirm before hard-to-reverse actions" rule.
3. Because it can contain hidden instructions (prompt injection) — treating it as data, never as commands, prevents the agent from being hijacked into unintended actions.
4. A golden set is a fixed list of representative questions/inputs with expected answers, run automatically on every change — it mirrors Day 157's model/regression-test concept applied to the LLM system.
5. Strict validation of the tool's argument (rejecting anything not syntactically plausible) and rejecting inputs exceeding a length limit.

---

## Day 164 — LLM Cost/Perf Ops: Caching, Batching, Quantization — FinOps for AI

**Warm-up:**
1. The LLM proposes a tool and its arguments; the agent's own code decides whether and how to execute it.
2. They can't change state or cause damage if something goes wrong; state-changing tools need human confirmation.
3. A golden set is a fixed set of question/expected-answer pairs run automatically on every change; it protects against silent regressions in prompt/model/RAG-index quality.
4. Strict schema/format validation on the argument, and rejecting over-length or malformed input.

**Recall:**
1. Exact-match caching returns a stored response only for an identical prompt (cheap, safe); semantic caching matches by meaning/similarity above a threshold (higher hit rate, but risks returning a stale/wrong answer for a subtly different query).
2. Batching matters more on GPU because GPU servers like vLLM get real throughput gains from continuous batching of concurrent requests; on CPU with Ollama, concurrency is much more limited, so batching gains are minimal.
3. Quantization trades numeric precision (and thus some answer quality) for a smaller memory footprint and faster inference; documented benchmarks must be checked because quality loss varies by model/quantization level and can't be assumed "good enough."
4. Picking the smallest model that meets the quality bar, and capping output length (`max_tokens`).
5. An idle or underused GPU is pure wasted spend — utilization (like rightsizing elsewhere) is the lever that turns GPU cost into value, and autoscaling-to-zero avoids paying for idle time.

---

## Day 165 — AIOps I: ★ Anomaly Detection on the Project's Prometheus Metrics

**Warm-up:**
1. Data drift is a shift in the input feature distribution; prediction drift is a shift in the distribution of the model's own outputs (a cheap, label-free proxy).
2. Picking the smallest model that meets the quality bar, and capping output length.
3. From the project's real Prometheus metrics, pulled via PromQL/the HTTP API.
4. A changed hash on a stage's declared dependencies (code, data, or params).

**Recall:**
1. Threshold-based alerting only catches values crossing a fixed line; it misses anomalies that are unusual for that metric's own normal pattern (e.g. a value that's fine most of the time but never occurs in a given context) without a fixed threshold.
2. Because shuffling would let the model "see the future" — train on data that chronologically follows the test set — inflating apparent performance and breaking real-world causal ordering.
3. `contamination` represents the expected proportion (rate) of anomalies in the data, which the model uses to calibrate how aggressively it flags outliers.
4. It establishes a simple, explainable reference before reaching for a fancier model, so the added complexity of Isolation Forest can be judged against a known baseline.
5. `detect.py`'s CronJob produces detected anomalies (timestamps + scores) from live metrics; Day 166 wires this output into Alertmanager as a real alert.

---

## Day 166 — AIOps II: Log Clustering, Alert-Noise Reduction, Wiring Anomalies into Alertmanager

**Warm-up:**
1. Because it misses anomalies unusual for a metric's own normal pattern rather than a value crossing a fixed threshold.
2. The expected proportion (rate) of anomalies in the data.
3. To avoid training on data chronologically after the test set, preserving real-world causal order.
4. Detected anomalies (timestamps + scores) from scoring live metrics.

**Recall:**
1. Log clustering groups by "template" (variable tokens like IDs/numbers/IPs replaced with placeholders) because exact-match grouping is useless against huge-cardinality unstructured logs — structurally similar lines need to collapse into one group.
2. Two paths: (a) expose anomaly results as Prometheus metrics plus a normal Prometheus alerting rule, or (b) post directly to Alertmanager's HTTP API. The lab used (a), for consistency with the rest of the existing alerting stack.
3. Debouncing (requiring N consecutive scrapes) avoids firing on a single noisy blip, same reasoning as any other alert rule.
4. It reduces on-call cognitive load by surfacing one incident signal (root cause) instead of many separate pages for downstream symptoms.
5. It proved the full path worked end to end: a real (triggered) anomaly → detection → Prometheus alert fires → Alertmanager routes it → the runbook's first step actually helps diagnose it.

---

## Day 167 — Capstone Spec Day: Scope, Architecture Review, Success Criteria, 8-Day Build Plan

**Warm-up:**
1. GitOps-managed, meshed, SLO'd, chaos-tested.
2. The anomaly-detection model (Day 165); it plugs into Alertmanager via `anomaly_score`/`anomaly_detected` Prometheus metrics and a Prometheus alerting rule (Day 166).
3. ⚠ Session-specific — not named in the file. Illustrative candidates: Terraform/IaC (Phase 4), core shell scripting (Phase 1), or CKA-style live k8s debugging (Phase 3); the trainer should substitute the fresher's actual unexercised skills.

**Trainer-review questions:**
1. Reusing existing infra keeps the 8-day plan realistic and lets every build day land on already-proven infrastructure (cluster, GitOps, observability, ML component) instead of re-deriving it.
2. Day 175 (REVIEW + capstone buffer) is the dedicated day to catch up any deliverable that slipped across Days 169–174 before chaos/load testing begins.
3. Concretely: the anomaly-detection component must be demonstrably scoring this capstone service's own metrics (not a second, disconnected model) with a live, correctly-routed alert path — the exact bar closed on Day 174.
4. ⚠ Session-specific — a live judgment call for the trainer/fresher (e.g. an 8-day plan slipping, or a metric-transfer issue with the anomaly model); no fixed answer in the file.

---

## Day 168 — REVIEW (Days 162–167)

**Cumulative quiz:**
1. RAG grounds answers in retrieved private/current documents, which a plain LLM call (limited to training data + context) cannot access.
2. Because the query embedding must land in the same vector space as the indexed chunks for similarity search to work.
3. The agent proposes a tool call with arguments; your code decides whether and how to execute it.
4. Because it could contain hidden instructions (prompt injection) that could hijack the agent into unintended actions.
5. Exact-match caching returns a cached response only for identical prompts (safe, lower hit rate); semantic caching matches by meaning (higher hit rate, risk of returning a wrong answer for a subtly different query).
6. Picking the smallest adequate model, and capping output length (`max_tokens`).
7. To avoid training on data chronologically after the test set, preserving real-world causal order.
8. The expected proportion (rate) of anomalies in the data.
9. The Prometheus-metrics path (expose `anomaly_score`/`anomaly_detected`, scrape, write a normal alerting rule) — used for consistency with the existing alerting stack.
10. Scope decision: a second, small service scaffolded via the Backstage golden path (default recommendation), reusing the existing cluster/GitOps/observability/ML stack so every build day lands on already-proven infrastructure instead of re-deriving it.

---

## Day 169 — Capstone Build 1: Repo Scaffold (Backstage Template) + Infra (Terraform)

*No "Recall questions" section in this build-sprint day; it has a Warm-up and Trainer-review questions instead of an end-of-day quiz.*

**Warm-up:**
1. ⚠ Session-specific — the actual choice recorded in `capstone-spec.md` (default recommendation: a second small service via the Backstage golden path, vs. a standalone utility app).
2. The 8 success criteria in one phrase each: (1) repo scaffolded via Backstage template, CI green; (2) infra in Terraform, no manual steps; (3) deployed via Helm + GitOps; (4) dashboards + 2 SLOs with burn-rate alerts; (5) security baseline — no secrets in git, scans, one OPA policy; (6) ML/AIOps anomaly detection visibly integrated; (7) survives one chaos + one load test (or the violation is explained/remediated); (8) documented and demoed on video.
3. It reuses cluster/GitOps/ML infra so the 8-day plan is realistic and every day builds on already-proven infrastructure instead of re-deriving it.

**Trainer-review questions:**
1. ⚠ Session-specific — the fresher shows the actual Backstage-generated CI workflow and its steps (lint/test at minimum).
2. Terraform state must be remote (not a local `.tfstate`) so it isn't lost or corrupted on one machine, and so it can be safely shared/locked for team or CI use — the same remote-state discipline as Day 81.
3. ⚠ Session-specific judgment call — typically: remove or adapt generated boilerplate that doesn't fit the capstone's scope, documenting why.
4. The blast radius is scoped to the capstone-specific infra/namespace; the rest of the platform is protected by having separate Terraform state/resources and by plan-before-apply review.

---

## Day 170 — Capstone Build 2: Core Services + CI

*No "Recall questions" section; Warm-up and Trainer-review questions only.*

**Warm-up:**
1. Repo public on GitHub, CI green, and Terraform state remote with no drift after apply — a go/no-go gate.
2. In remote state (per Day 169's setup), not a local `.tfstate` file — this matters for team/CI safety and locking.
3. ⚠ Session-specific — whatever the fresher kept vs. would have removed from the Backstage-generated scaffold.

**Trainer-review questions:**
1. ⚠ Session-specific — the fresher walks through a specific test and the logic path it protects.
2. Deliberately add a vulnerable dependency and show the scan step failing the build, then remove it and show the build passing — proving the gate isn't a silent no-op.
3. ⚠ Session-specific — whatever shortcut is flagged, with a plan to close it before Day 177's demo.
4. ⚠ Session-specific architectural judgment, dependent on the chosen service's design.

---

## Day 171 — Capstone Build 3: k8s/Helm Deploy + GitOps

*No "Recall questions" section; Warm-up and Trainer-review questions only.*

**Warm-up:**
1. Lint → test → build image → scan → push to registry.
2. By deliberately adding a vulnerable dependency and confirming the scan step fails the build, then removing it and confirming it passes.
3. ⚠ Session-specific — whatever shortcut was flagged in Day 170's review.

**Trainer-review questions:**
1. `argocd app diff` would show the drift (hand-edited fields differing from git); ArgoCD's self-heal would automatically revert the live state back to match git.
2. ⚠ Session-specific — the fresher walks through: introduce a bad image tag/config → sync → observe failure → roll back via ArgoCD (not by hand) → confirm recovery.
3. CI stops at updating the manifest/image tag because GitOps (ArgoCD), not CI, should own actual delivery — this keeps git as the single source of truth and lets ArgoCD control sync/rollback/self-heal.
4. ⚠ Session-specific — the fresher states the current resource request/limit and how it was chosen (e.g. observed load or a documented default).

---

## Day 172 — Capstone Build 4: Observability + SLOs

*No "Recall questions" section; Warm-up and Trainer-review questions only.*

**Warm-up:**
1. That the deployment had no drift — it's fully owned/reconciled by ArgoCD.
2. ⚠ Session-specific, e.g. "broke a config value on purpose, synced it, watched it fail, then rolled back via ArgoCD and confirmed automatic recovery."
3. Because GitOps should own delivery — CI's job stops at updating the manifest/tag in git; ArgoCD then syncs, keeping git as the single source of truth.

**Trainer-review questions:**
1. ⚠ Session-specific — the two chosen SLOs (e.g. availability, latency) and why they matter for this service.
2. ⚠ Session-specific — the fresher demonstrates the burn-rate alert firing from injected synthetic load/errors.
3. ⚠ Session-specific, per the day's written runbook — the documented first diagnostic steps.
4. ⚠ Session-specific — e.g. tracing, if not yet added.

---

## Day 173 — Capstone Build 5: Security + Policy + Secrets

*No "Recall questions" section; Warm-up and Trainer-review questions only.*

**Warm-up:**
1. ⚠ Session-specific — the two SLOs defined Day 172 (e.g. availability, latency) and their rationale.
2. ⚠ Session-specific — whatever traffic/error pattern triggered the burn-rate alert, and whether the runbook helped.
3. ⚠ Session-specific — whatever observability gap was flagged (e.g. missing tracing).

**Trainer-review questions:**
1. ⚠ Session-specific — the fresher walks through the actual secret-delivery path (e.g. a k8s Secret mounted as an env var/volume, or Vault/SOPS-managed).
2. ⚠ Session-specific — the fresher shows the exact Gatekeeper admission-webhook rejection error for a violating manifest.
3. Remediating a leaked secret requires rotating it and, if needed, scrubbing git history — this requires explicit approval before acting because rewriting history/force-pushing is a hard-to-reverse, disruptive action, echoing the OS's "confirm before hard-to-reverse actions" rule.
4. ⚠ Session-specific — the fresher's prioritized next security gap.

---

## Day 174 — Capstone Build 6: ML/AIOps Component Integration

*No "Recall questions" section; Warm-up and Trainer-review questions only.*

**Warm-up:**
1. ⚠ Session-specific — whatever the Day 173 secrets audit found (ideally nothing, or a remediated finding).
2. ⚠ Session-specific — the fresher describes the exact Gatekeeper rejection in one sentence.
3. The anomaly-detection model currently pulls its metrics from the original fresher project's Prometheus (the Day 152/165 data source).

**Trainer-review questions:**
1. ⚠ Session-specific — the reuse-vs-second-instance decision and rationale (reuse if the capstone's metric statistics are similar/overhead-light; a second instance if isolation or differing statistics justify it).
2. A model trained on one metric's statistical properties (mean, variance, seasonality) may not transfer to a different metric with different baseline behavior — its threshold/contamination assumptions could misfire, so retraining on the new metric's own distribution is safer.
3. ⚠ Session-specific — the synthetic anomalous traffic generated and the alert that fired.
4. "Visibly integrated" now means the anomaly detector is provably scoring this capstone service's own metric data, with a live end-to-end drill (synthetic anomaly → alert → routing) observed by the trainer.

---

## Day 175 — REVIEW + Capstone Buffer

**Cumulative quiz:**
1. Repo public on GitHub, CI green, and capstone-specific Terraform infra applied with clean remote state — a go/no-go gate.
2. Lint → test → build image → scan → push to registry.
3. It reveals drift from someone hand-editing the live deployment (fields differing from git); ArgoCD's self-heal would auto-revert it.
4. ⚠ Session-specific two SLOs (e.g. availability and latency), chosen for measurable, user-facing reliability of the service.
5. The secrets audit checked for plaintext secrets in git history/manifests (e.g. via `git log -p` or `gitleaks`); remediation for a leaked secret requires rotating it and scrubbing git history, with explicit approval before any history-rewriting force-push (a hard-to-reverse action).
6. Because a model's threshold/contamination assumptions are tuned to one metric's statistical properties (mean/variance/seasonality), which may not hold for a different metric.
7. Success criterion 6 requires the anomaly-detection component to be visibly, demonstrably integrated with this capstone service's own telemetry — not a second, disconnected model.
8. ⚠ Session-specific — whichever was chosen (extending the existing CronJob to also score the capstone's metric, or a second, clearly-named instance) and the reuse-vs-isolation rationale given.
9. CI's job is to build/test/scan/push an image and update the manifest/tag in git; GitOps (ArgoCD)'s job is to detect that change and sync/apply it to the live cluster, owning actual delivery.
10. Deliberately introducing a vulnerable dependency and confirming the scan step failed the build, then removing it and confirming it passed — proving the gate wasn't a silent no-op.

---

## Day 176 — Capstone Build 7: Chaos Test + Load Test + Hardening

*No "Recall questions" section; Warm-up and Trainer-review questions only.*

**Warm-up:**
1. ⚠ Session-specific — whatever open checkpoint, if any, Day 175's buffer closed.
2. ⚠ Session-specific — the two SLOs defined on Day 172.
3. ⚠ Session-specific — should have been reconfirmed live per Day 175's buffer instructions.

**Trainer-review questions:**
1. ⚠ Session-specific — which alert (SLO burn-rate or `AnomalyDetected`) fired during the chaos experiment, and whether that was the correct behavior given the injected fault.
2. ⚠ Session-specific — the identified saturation point and bottleneck resource (CPU, memory, connections, or a downstream dependency).
3. The fresher reruns the relevant test after applying the fix and shows the metric/behavior actually improved, rather than assuming it did from the code change alone.
4. ⚠ Session-specific — the next-highest-risk weakness the fresher would test with more time.

---

## Day 177 — Capstone Docs + Demo Video

*No "Recall questions" section; Warm-up and Trainer-review questions only.*

**Warm-up:**
1. ⚠ Session-specific — the Day 176 chaos finding and which alert fired.
2. ⚠ Session-specific — Day 176's load-test saturation point/bottleneck.
3. ⚠ Session-specific — Day 176's hardening fix and how it was verified.

**Trainer-review questions:**
1. ⚠ Session-specific — the fresher shows live evidence (dashboard screenshot, alert log, commit) backing one checked success criterion.
2. ⚠ Session-specific — whichever part of the demo needed a retake, and why.
3. ⚠ Session-specific — the fresher's chosen single takeaway for an interviewer.
4. ⚠ Session-specific — whatever isn't yet "portfolio-perfect."

---

## Day 178 — Facilitator Script: Job Sprint

No warm-up or recall/quiz questions in this day file — it is a facilitator session script (resume finalization, LinkedIn rewrite, 20 applications, referral asks) with a facilitator checklist instead of a quiz.

---

## Day 179 — Facilitator Script: Final Mock-Interview Marathon

No warm-up or recall/quiz questions in this day file — it is a facilitator-run mock-interview script (technical, HR/behavioral, salary/negotiation rounds) with a facilitator checklist instead of a quiz.

---

## Day 180 — Facilitator Script: Graduation, Retrospective, 90-Day Job-Hunt Plan

No warm-up or recall/quiz questions in this day file — it is the closing facilitator script (capstone presentation, program retrospective, 90-day job-hunt plan, what-to-learn-next map) with a facilitator checklist instead of a quiz.
