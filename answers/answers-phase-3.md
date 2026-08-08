# Answer Key — Phase 3 (Days 61–90)

Trainer prep companion. Answers are grounded in each day's theory section; ⚠
marks answers drawn from general knowledge beyond the day file — verify
against official docs before teaching.

---

## Day 061 — Why Orchestration; Kubernetes Architecture

**Warm-up:**
1. commit → build/test (CI) → image scan → deploy (Ansible to VM).
2. It checks the image's OS packages/dependencies against known CVE databases before it ships.
3. So repeated runs converge to the same correct state without duplicating actions or causing side effects on re-run.
4. Image = the immutable build artifact (filesystem + metadata); container = a running/stopped instance of an image with a writable layer. Matters because a crashed container can be discarded and a fresh one started from the same unchanged image — no data is lost from the image itself.

**Recall:**
1. Control-plane: kube-apiserver (front door for all REST calls), etcd (source-of-truth key-value store), kube-scheduler (assigns pods to nodes), kube-controller-manager (runs reconciliation controllers). Node: kubelet (ensures containers match PodSpecs), kube-proxy (Service networking rules), container runtime (containerd/CRI-O, runs containers).
2. etcd.
3. `apiVersion`, `kind`, `metadata`, `spec`.
4. A controller continuously observes actual state, compares it to the desired state stored in etcd, and acts to reconcile any difference — forever, not just once.
5. It was a bare Pod with no controller (no ReplicaSet/Deployment) watching it, so nothing recreated it after deletion.
6. `kubectl apply` — declarative, matches the IaC mindset; `create` is imperative and fails if the resource already exists.

---

## Day 062 — kubectl Fluency; Pods Deep

**Warm-up:**
1. etcd.
2. `apiVersion`, `kind`, `metadata`, `spec`.
3. `kubectl apply` — declarative diff+patch, matching the IaC mindset from Phase 2.
4. It was a bare Pod with no controller to recreate it.

**Recall:**
1. Same network namespace (same IP, reach each other via `localhost`) and can share volumes.
2. Init containers run to completion, in order, BEFORE app containers start; sidecars run alongside the main container for the Pod's whole lifecycle.
3. `describe` shows human-readable detail plus Events (the scheduling/pull/start narrative) that `-o yaml` (raw spec/status only) doesn't include.
4. Label — labels drive selection; annotations are free-form metadata not used for selection.
5. `Pending` (shown concretely as `Init:0/1` etc.) — the Pod isn't `Running` until all init containers finish.
6. `kubectl get pod <name> -o jsonpath='{.status.podIP}'`.

---

## Day 063 — REVIEW

**Cumulative quiz:**
1. commit → build/test (CI) → scan (trivy/docker scan) → deploy (Ansible to VM).
2. Secrets belong in a secrets manager / CI secrets store, injected at runtime; they must NEVER go into source control, committed files, or baked into images.
3. kube-apiserver, etcd, kube-scheduler, kube-controller-manager.
4. etcd is the cluster's single source of truth — the distributed key-value store holding all object state.
5. Under a Deployment: the ReplicaSet controller notices actual < desired and creates a replacement automatically. Bare Pod: nothing recreates it — it stays gone.
6. `apply` — declarative and idempotent, matching IaC practice; `create` is imperative and errors if the object already exists.
7. Init container: runs to completion, in order, before app containers start. Sidecar: runs alongside the app container for the Pod's full lifetime.
8. The Pod's network namespace (same IP, reachable via `localhost`) and volumes.
9. Label — drives selection.
10. `kubectl logs <pod> --previous`.

---

## Day 064 — Deployments and ReplicaSets

**Warm-up:**
1. observe → compare (to desired state) → act (reconcile).
2. It was a bare Pod with no controller to recreate it.
3. AND semantics — a comma-separated selector must match all listed labels.
4. `-f` follows the current container's live logs; `--previous` shows the last crashed/previous container instance's logs.

**Recall:**
1. Deployment → ReplicaSet → Pod → container (via kubelet/runtime).
2. Because the ReplicaSet uses `selector.matchLabels` to identify which Pods it owns; if it doesn't match `template.metadata.labels`, the ReplicaSet can't recognize/manage the very Pods it creates.
3. RollingUpdate overlaps old/new Pods (zero downtime by design); Recreate kills all Pods before starting new ones (downtime) — chosen when two app versions can't safely run concurrently.
4. `kubectl rollout history deployment/<name>`; entries become readable via the `kubernetes.io/change-cause` annotation (or the deprecated `--record` flag).
5. The ReplicaSet controller notices actual < desired and creates a replacement Pod automatically — the Day 61 reconciliation loop in action.
6. `kubectl rollout undo deployment/<name> --to-revision=N`.

---

## Day 065 — Services & the Kubernetes Networking Model

**Warm-up:**
1. Deployment → ReplicaSet → Pod → container.
2. Recreate — kills all Pods before creating new ones; RollingUpdate overlaps old/new to avoid downtime.
3. The ReplicaSet controller recreates it automatically (self-healing).
4. Because `template.metadata.labels` must match `selector.matchLabels` for the controller to recognize and manage its own Pods.

**Recall:**
1. Pod IPs are ephemeral and change on every reschedule/rollout — a hardcoded IP would break as soon as the Pod is replaced.
2. kube-proxy — it programs iptables/IPVS rules on each node that DNAT traffic bound for a Service's ClusterIP to a real backing Pod IP.
3. `<service>.<namespace>.svc.cluster.local` (short form `<service>` works within the same namespace).
4. ClusterIP: internal-only stable IP. NodePort: opens a static port (30000–32767) on every node for external reach. LoadBalancer: provisions an external cloud load balancer.
5. It's removed from the Service's Endpoints list and stops receiving traffic, without being restarted.
6. Because kind's default CNI (kindnet) doesn't enforce NetworkPolicy — enforcement requires a policy-aware CNI (e.g. Calico).

---

## Day 066 — Ingress and an Ingress Controller

**Warm-up:**
1. `backend.default.svc.cluster.local` (short form `backend` works within the same namespace).
2. NodePort — reachable from outside the cluster by default; ClusterIP is internal-only.
3. kube-proxy/the Endpoints controller updates it, based on which Pods match the selector AND are passing readiness checks.
4. Because kind's default CNI (kindnet) doesn't enforce NetworkPolicy.

**Recall:**
1. The Ingress object is only a declarative routing-rules spec; the Ingress Controller (e.g. ingress-nginx, an actual running proxy) watches Ingress objects and implements the routing.
2. Because kind runs nodes as Docker containers that don't expose host ports 80/443 by default — the special node config maps container ports to host ports and labels the node `ingress-ready`.
3. `Prefix` matches the path and anything beneath it; `Exact` matches only that literal path.
4. A `kubernetes.io/tls` Secret containing `tls.crt`/`tls.key`.
5. Via multiple entries in `spec.rules[]`, each with its own `host` field routing to a different backend service, under one Ingress object.
6. At the Ingress controller (the proxy Pod) — it terminates TLS and proxies plaintext HTTP to the backend Pod.

---

## Day 067 — ConfigMaps & Secrets

**Warm-up:**
1. `ingressClassName` (e.g. `nginx`) — it tells k8s which Ingress controller should handle the Ingress, since a cluster can run multiple controllers.
2. Path-based routing varies `http.paths[].path`; host-based routing varies `rules[].host`.
3. `kubernetes.io/tls`.
4. Via a label selector matching Pod labels, which populates the Service's Endpoints list.

**Recall:**
1. Base64 is encoding, not encryption — anyone with `get secret` RBAC access can decode it instantly; real protection comes from RBAC restrictions, etcd encryption-at-rest, and external secret stores (Vault/sealed-secrets/sops).
2. envFrom/env[].valueFrom (environment variables), or mounted as files via `volumes[].configMap`.
3. Mounted files are re-synced by the kubelet within ~60s of a source change; env vars are fixed at container start and only pick up changes on a new rollout.
4. subPath mounts do NOT auto-update when the source ConfigMap/Secret changes.
5. Mounted files get restrictive permissions and don't leak into `docker inspect`/process listings the way env vars can.
6. RBAC restricting who can `get`/`list` Secrets, etcd encryption-at-rest, and external secret stores (Vault, sealed-secrets, sops).

---

## Day 068 — Persistent Storage: PV/PVC/StorageClass; StatefulSets

**Warm-up:**
1. Base64 encoding is not encryption — trivially decodable by anyone with RBAC read access.
2. Env-var-injected config doesn't update on a running Pod (fixed at container start); mounted files (without subPath) do update live.
3. envFrom/env[].valueFrom, or `volumes[].configMap` mounted files.
4. subPath mounts don't auto-update on a ConfigMap/Secret change.

**Recall:**
1. PVC is a Pod's request for storage; StorageClass is the template telling the provisioner how/what to dynamically create; PV is the actual provisioned storage the PVC binds to.
2. ReadWriteOnce.
3. `Retain` — when data must survive accidental PVC/namespace deletion (requires manual cleanup afterward).
4. Stable network identity (predictable Pod names + stable per-Pod DNS) and stable per-replica storage (own PVC, not shared).
5. To give each replica a stable, predictable per-Pod DNS name (`pod-N.service`) rather than one load-balanced endpoint.
6. Because StatefulSet PVCs are designed to survive by default for data safety — deleting the StatefulSet doesn't cascade-delete its PVCs.

---

## Day 069 — Health & Resources: Probes, Requests/Limits, HPA

**Warm-up:**
1. PV = the actual provisioned storage object; PVC = a Pod's request for storage; StorageClass = the template defining which provisioner/how PVs are dynamically created.
2. Stable network identity plus stable per-replica storage, with ordered creation/deletion.
3. Because StatefulSet PVCs are meant to survive deletion of the StatefulSet, for data safety.
4. `Retain` — when data must survive accidental deletion.

**Recall:**
1. Liveness failure → kubelet restarts the container. Readiness failure → Pod removed from Service Endpoints (via kube-proxy), not restarted.
2. It gives a slow-starting app a grace period before liveness/readiness checks begin, so a legitimately slow boot isn't mistaken for a hang and crash-looped.
3. CPU limit exceeded → throttled (CFS quota, not killed). Memory limit exceeded → the container is OOMKilled (no throttling possible for memory).
4. Guaranteed (requests == limits for every container), Burstable (requests < limits), BestEffort (no requests/limits set) — determined by the requests/limits configured on the Pod's containers.
5. metrics-server must be installed (not part of core k8s); on kind it needs `--kubelet-insecure-tls` since kind's kubelet certs aren't signed for that check by default.
6. Because requests are what the scheduler uses to decide if a node has room (a placement guarantee); limits are a runtime enforcement, irrelevant to scheduling.

---

## Day 070 — REVIEW

**Cumulative quiz:**
1. Deployment → ReplicaSet → Pod → container.
2. RollingUpdate = default, zero-downtime overlapping rollout; Recreate = kill-all-then-start-all (downtime), used when two versions can't coexist.
3. ClusterIP = internal-only; NodePort = static port on every node for external access; LoadBalancer = cloud-provisioned external LB.
4. kube-proxy (programs iptables/IPVS rules).
5. Ingress object = declarative routing-rule spec; Ingress controller = the actual proxy implementing it.
6. Base64 is encoding, not encryption — trivially decodable by anyone with read access; real protection needs RBAC, etcd encryption-at-rest, or external secret stores.
7. PV = actual provisioned storage; PVC = a Pod's request for storage; StorageClass = the dynamic-provisioning template.
8. To give each replica a stable, predictable per-Pod DNS name instead of one load-balanced Service endpoint.
9. Liveness failure → kubelet restarts the container. Readiness failure → Pod pulled from Service Endpoints, not restarted.
10. Crossing a metric threshold (default CPU utilization %) watched via metrics-server, which must be installed first (with `--kubelet-insecure-tls` on kind).

---

## Day 071 — RBAC, ServiceAccounts, Security Contexts

**Warm-up:**
1. Base64 is encoding, not encryption — trivially reversible by anyone with read access.
2. It's removed from the Service's Endpoints (stops receiving traffic) without being restarted.
3. Requests — used by the scheduler for placement decisions.
4. Guaranteed, Burstable, BestEffort.

**Recall:**
1. Role is namespaced; ClusterRole is cluster-scoped (covers cluster-wide resources, or is reused across namespaces via bindings).
2. The Pod's ServiceAccount (the `default` SA in its namespace if none is specified), via its auto-mounted token.
3. Because `default` is the implicit identity for every unconfigured Pod in the namespace — granting it permissions broadens access to every such Pod, violating least privilege.
4. It enforces that the container's root filesystem cannot be written to; the common fix is mounting an `emptyDir` volume at the specific path the app needs to write.
5. Privileged, Baseline, Restricted.
6. Via a namespace label (`pod-security.kubernetes.io/enforce: restricted`) — enforced natively by the API server's built-in admission controller, no extra install needed.

---

## Day 072 — Scheduling Controls; DaemonSets; Jobs/CronJobs

**Warm-up:**
1. Namespaced (Role) vs cluster-scoped (ClusterRole).
2. The Pod's ServiceAccount (default `default` SA if none specified).
3. It enforces that the container's root filesystem cannot be written to.
4. Privileged, Baseline, Restricted.

**Recall:**
1. `requiredDuringScheduling...` = hard constraint (must match); `preferredDuringScheduling...` = soft preference (scheduler tries, won't fail if unmet).
2. Affinity is the Pod choosing/preferring nodes; taints/tolerations are nodes rejecting Pods by default unless the Pod explicitly tolerates the taint — the inverse relationship, and a toleration only permits (doesn't force) placement.
3. Because its replica count is implicitly "one Pod per eligible node," not a user-set number.
4. A CronJob creates Jobs on a cron schedule; each scheduled run produces a new Job object, and each Job runs a Pod to completion.
5. It prevents a new scheduled Job run from starting while the previous run is still active.
6. Because a Job's Pod is meant to run to completion, not be restarted forever — `Always` conflicts with that model, so only `OnFailure`/`Never` are valid.

---

## Day 073 — Troubleshooting Playbook: Events, Crashloops, Pending, DNS

**Warm-up:**
1. The field name itself signals it: `requiredDuringScheduling...` (hard) vs `preferredDuringScheduling...` (soft).
2. Affinity = Pod choosing/preferring nodes; taints/tolerations = nodes rejecting Pods by default unless tolerated — the inverse relationship.
3. To control what happens if a scheduled run overlaps with a still-active previous Job (`Allow`/`Forbid`/`Replace`).
4. Because Jobs run Pods to completion — `Always` would conflict with that "run once, finish" model; only `OnFailure`/`Never` are valid.

**Recall:**
1. `kubectl describe pod <name>` (to read Events).
2. No — the STATUS text alone doesn't explain why; you need `kubectl logs`/`kubectl logs --previous` plus `kubectl describe pod`'s Last State/Reason (e.g. OOMKilled, Error, exit code).
3. `kubectl logs <pod> --previous`.
4. `nslookup kubernetes.default` from a debug pod — confirms whether DNS itself works before assuming only the specific Service name is missing.
5. `CreateContainerConfigError`.
6. Readiness — the container is running but failing its readiness probe, not crashing or restarting.

---

## Day 074 — Helm I: Consuming Charts

**Warm-up:**
1. `kubectl describe pod <name>`, to read Events.
2. No, the status text alone doesn't explain why.
3. `kubectl logs <pod> --previous`.
4. `CreateContainerConfigError`.

**Recall:**
1. A chart is the packaged, versioned template (definitions + defaults); a release is a named, tracked instance of that chart actually installed into a cluster.
2. It renders the chart's manifests locally without installing/applying anything — used to preview exactly what an install/upgrade would apply before running it.
3. `helm get values <release> --all`.
4. Chart's own `values.yaml` (lowest) → `-f` file(s) in the order given → `--set` flags (highest, last one wins on conflicts).
5. The whole release — it reverts every resource the release manages to a previous revision, not just one object.
6. Via labels/annotations Helm applies to every resource it creates, plus a stored release manifest (kept in a Secret) that tracks ownership.

---

## Day 075 — Helm II: Author a Chart for the Project

**Warm-up:**
1. Release = a named, tracked, installed instance; chart = the packaged, versioned template itself.
2. `helm template . | kubectl apply --dry-run=server -f -` — render locally, then validate the rendered output against the live API server without creating anything.
3. `helm test <release>` runs Pods under `templates/tests/` (with a `helm.sh/hook: test` annotation) after install, to assert the release actually works.
4. Because it keeps every template's resource names/labels generated consistently, so `kubectl get -l app.kubernetes.io/instance=<release>` reliably finds everything, and naming logic isn't duplicated/drifting across templates.

**Recall:**
1. A working starter skeleton (Deployment, Service, Ingress, HPA, ServiceAccount templates, `values.yaml`, `Chart.yaml`, `_helpers.tpl`, tests) instead of hand-building correct chart structure from a blank directory.
2. Because `_`-prefixed files are template libraries only (helper definitions), not standalone manifests meant to be rendered.
3. `helm template` (render) then `kubectl apply --dry-run=server -f -` (server-side validation) before actually installing/upgrading.
4. Pods defined under `templates/tests/` with a `helm.sh/hook: test` annotation, run after install as a smoke test.
5. It converts an arbitrary nested values block (e.g. a whole `resources` object) into correctly indented YAML at a given nesting level inside a template.
6. It ensures every template generates the same resource names/labels, so tooling and humans can reliably select/find all objects belonging to one release.

---

## Day 076 — Kustomize: Bases, Overlays; Helm vs Kustomize

**Warm-up:**
1. A working starter chart skeleton (Deployment/Service/Ingress/HPA/ServiceAccount templates, `values.yaml`, `Chart.yaml`, `_helpers.tpl`, tests).
2. `helm template` then `kubectl apply --dry-run=server -f -`.
3. Pods under `templates/tests/` with a `helm.sh/hook: test` annotation, run after install.
4. It keeps resource naming/labeling consistent across every template in the chart.

**Recall:**
1. Helm templates YAML with a templating language (fill in "holes" with values); Kustomize patches plain, valid k8s YAML bases via declarative overlays — no template language at all.
2. It changes whenever the ConfigMap/Secret content changes, which forces any referencing Deployment's Pod template to change too, automatically triggering a rolling update — solving the "env vars don't hot-reload" problem.
3. `patchesStrategicMerge` merges by field (common, intuitive); `patchesJson6902` is needed for surgical operations strategic merge can't express, like removing a specific array element.
4. No new templating language required — just plain k8s YAML plus a `kustomization.yaml` recipe file.
5. Favor Helm: distributing a reusable package / consuming third-party software (e.g. bitnami charts). Favor Kustomize: you own both the base and all environments and want plain-YAML overlays (e.g. GitOps environment promotion).
6. `kubectl kustomize <dir>` (or the standalone `kustomize build <dir>`) for Kustomize; `helm template` is Helm's equivalent.

---

## Day 077 — REVIEW

**Cumulative quiz:**
1. Role is namespaced; ClusterRole is cluster-scoped.
2. It enforces that the container's root filesystem cannot be written to.
3. Taints/tolerations = nodes reject Pods by default unless tolerated; affinity = Pods choose/prefer nodes — the inverse relationship.
4. A CronJob schedules Jobs on a cron schedule; each scheduled run produces a new Job, and each Job runs a Pod to completion.
5. `kubectl describe pod <name>` — because k8s Events almost always explain what's wrong in plain English, avoiding guesswork.
6. `nslookup kubernetes.default` from a debug pod — if that resolves, DNS itself is healthy and only the specific Service name/record is the problem.
7. Chart = the packaged, versioned template; release = a named, tracked, installed instance of that chart.
8. Consistent resource naming/labeling across every template, so selection (`-l app.kubernetes.io/instance=`) works reliably for the whole chart.
9. It forces a new hash-suffixed ConfigMap name whenever content changes, which automatically triggers a rolling update on any Deployment referencing it.
10. Favor Helm for distributing a reusable package or consuming third-party charts; favor Kustomize when you own the base and all environments and want plain-YAML overlays without a templating language.

---

## Day 078 — ★ Milestone: Full App on kind via Helm Chart, Deployed from CI

**Warm-up:**
1. A chart is the packaged, versioned template; a release is a named, tracked, installed instance of it.
2. ⚠ Not stated explicitly in this day's theory — per Day 76's decision framework, Helm was chosen because the team owns the app and wants first-class versioned releases with rollback, and CI-driven deploys benefit from a single idempotent install/upgrade command.
3. `helm upgrade --install` creates the release if absent or upgrades it if present, making the command idempotent — required for CI, which can't assume whether a release already exists (mirrors Ansible's idempotency principle).
4. `helm template <chart>` — renders manifests locally without installing anything.

**Recall:**
1. Because CI can't know in advance whether the release already exists — `--install` makes the same command safely create-or-update.
2. So CI actually waits for resources to reach Ready, verifying real success rather than just that manifests were accepted by the API server.
3. Because it proves the chart deploys cleanly from zero every time with no accumulated drift — a stronger test than upgrading a long-lived, possibly-drifted cluster.
4. By passing `--set image.tag=${{ github.sha }}` (or an equivalent build identifier) into the Helm values, so the exact image just built and scanned is the one deployed.
5. That the app actually responds/functions correctly end-to-end — `--wait` only proves resources reached Ready, not that the application logic works.
6. App (Deployment), database (StatefulSet or Deployment+PVC), networking (Service + Ingress), and the CronJob (reporting).

---

## Day 079 — Terraform I: HCL, Providers, init/plan/apply/destroy, State

**Warm-up:**
1. `helm upgrade --install` matters because CI can't know if the release exists — it idempotently creates or upgrades.
2. So CI verifies resources actually reached Ready, not just that they were submitted.
3. App, database, networking (Service + Ingress), CronJob.
4. That the app actually responds correctly (a real functional check), beyond resources merely reaching Ready.

**Recall:**
1. It computes the diff between current state and desired config without changing anything — read it fully before applying since it's the last chance to catch unintended changes or destructions.
2. State maps HCL resources to real infrastructure IDs and can contain sensitive attributes in plaintext — it must never be committed because it can leak secrets and enables state corruption/conflicts if shared via git.
3. `.terraform.lock.hcl` (dependency lock file) SHOULD be committed; `terraform.tfstate` must NEVER be committed (gitignored).
4. The next `terraform plan` shows a diff and proposes to reconcile the drift back to what's declared in HCL, since Terraform assumes it owns anything recorded in its state.
5. Terraform provisions/creates infrastructure resources; Ansible configures what's installed and running inside them.
6. It tears down everything Terraform manages in the config — it deserves the same review discipline as `apply` because it's destructive and hard to reverse.

---

## Day 080 — Terraform II: Variables, Outputs, Locals, Modules, Data Sources

**Warm-up:**
1. `terraform plan` computes the diff between current and desired state; reading it before `apply` catches mistakes before they touch real infrastructure.
2. `.tfstate` must never be committed; `.terraform.lock.hcl` should be committed alongside it.
3. The next `plan` shows a diff and proposes to reconcile the drift back to the declared config.
4. Terraform provisions infrastructure; Ansible configures what runs on/in it.

**Recall:**
1. CLI `-var` wins (highest precedence), then `.tfvars` file(s), then variable defaults (lowest).
2. A variable is user-supplied input; a local is a named, computed expression derived from other values — used to avoid repeating a computed expression across multiple resource blocks.
3. `variables.tf` (inputs), `main.tf` (resources), `outputs.tf` (what it exposes).
4. `module.<name>.<output_name>`.
5. `resource` creates/owns/manages a piece of infrastructure; `data` only reads existing information without managing it.
6. Because the attribute used to derive the resource's name (via the local) may be one the provider treats as immutable/force-new, requiring destroy-then-recreate.

---

## Day 081 — Terraform III: Remote State & Locking, Workspaces, Import, Drift

**Warm-up:**
1. CLI `-var` wins, then `.tfvars` files, then defaults.
2. `variables.tf`, `main.tf`, `outputs.tf`.
3. `resource` owns/manages; `data` only reads.
4. ⚠ Not stated generically in this day's theory — some provider attributes (like `name`) are immutable/force-new, so changing the value feeding it via a local can trigger destroy+recreate rather than an in-place update.

**Recall:**
1. Remote state + locking lets multiple people/processes share one source of truth and blocks a second `apply` while one is already running, preventing concurrent corruption — local state can do neither.
2. Because backend configuration must be resolved before variables are — a chicken-and-egg problem, so backend blocks can't reference variables.
3. State only — you must still hand-write the matching `resource` block in HCL yourself; `import` doesn't generate configuration.
4. By applying/destroying in one workspace and confirming resources in the other workspace remain untouched (each workspace tracks its own separate state).
5. Workspaces share the same backend config and provider credentials — for environments needing genuinely separate access boundaries (e.g. different AWS accounts), separate root configs/directories are usually safer.
6. Running `terraform plan` regularly (even on a CI schedule) and alerting if it shows a non-empty diff, catching drift early.

---

## Day 082 — Terraform IV: Local Practice, Lifecycle, count/for_each

**Warm-up:**
1. Remote state + locking enables safe shared/concurrent use and prevents state corruption from simultaneous applies.
2. Because backend config must be known before variables are resolved (chicken-and-egg problem).
3. State only.
4. By destroying/applying in one workspace and confirming the other workspace's resources were unaffected.

**Recall:**
1. `count` indexes replicas by POSITION — removing a middle item shifts every index after it, causing Terraform to destroy/recreate resources that didn't actually change.
2. `for_each` tracks each instance by a stable key rather than position, so removing one item only affects that one; the rest show no changes.
3. It blocks a critical resource from being destroyed (via `destroy` or any plan that would remove it) — deliberately overridden by removing the `prevent_destroy` flag first.
4. It creates the replacement resource BEFORE destroying the old one, instead of the default destroy-then-create — avoiding downtime for a replacement-forcing change.
5. When an attribute is managed by another system and you want Terraform to stop flagging drift on it — risky because it creates a permanent blind spot for that attribute.
6. Use `for_each` whenever items have meaningful identity (names/environments); reserve `count` for truly identical, order-independent replicas or a simple on/off (0/1) pattern.

---

## Day 083 — IaC Discipline: Terraform/Ansible Boundary; fmt/validate/tflint in CI

**Warm-up:**
1. Removing a middle list item shifts positional indices, causing Terraform to destroy/recreate unrelated resources that didn't actually change.
2. `for_each` keys instances by stable identity, not position, so removing one item only affects that item.
3. It protects a resource from being destroyed by `destroy` (or a destructive plan), forcing deliberate removal of the flag before it can be destroyed.
4. When a resource change would normally force destroy-then-create and the downtime from that ordering is unacceptable — create the replacement first, then destroy the old one.

**Recall:**
1. Terraform: creates/destroys/tracks the existence of infrastructure resources. Ansible: takes an existing target and ensures its internal configuration (packages, users, files, services) matches a desired state.
2. Because it's fragile, offers no idempotency guarantees, and has poor error handling — configuration should be handled by a purpose-built tool (Ansible) instead.
3. `validate` checks syntax/internal consistency without needing provider credentials or touching infrastructure; `fmt` only checks/fixes formatting — neither substitutes for the other.
4. Provider-specific mistakes `validate` doesn't catch: deprecated arguments, invalid instance types, unused declared variables.
5. `fmt -check` → `validate` → `tflint` → `plan` (posted for human review) → `apply` (only on merge, ideally with manual approval) — `apply` never runs automatically on a PR because it's destructive/irreversible and needs human review of the plan first.
6. Terraform creates the infrastructure and outputs an identifier (e.g. an IP) → that output feeds Ansible's inventory → Ansible takes over configuration from there.

---

## Day 084 — REVIEW

**Cumulative quiz:**
1. Because CI can't know in advance whether the release exists — `--install` makes it idempotently create-or-upgrade.
2. App (Deployment), database, networking (Service + Ingress), CronJob.
3. A mapping of HCL resources to real infrastructure IDs (possibly including sensitive attributes) — must never be committed because it can leak secrets and cause corruption/conflicts if shared via git.
4. CLI `-var` > `.tfvars` file > variable defaults.
5. `resource` owns/manages infrastructure; `data` only reads existing infrastructure without managing it.
6. It lets multiple people/processes share state safely and prevents concurrent applies from corrupting it via locking.
7. State only — the matching HCL `resource` block must still be hand-written.
8. Because `for_each` keys by stable identity, so changing/removing one item doesn't cause unrelated items to be destroyed/recreated (unlike `count`'s positional indexing).
9. It guards a critical resource against being destroyed by `destroy` or a destructive plan.
10. Terraform provisions infrastructure; Ansible configures what's installed and running on it.

---

## Day 085 — CKA Drill Day I: Timed Cluster Tasks

Drill day — contains collapsed solutions in the day file itself; no separate answer key needed.

---

## Day 086 — CKA Drill Day II: Troubleshooting, RBAC, etcd Backup/Restore

Drill day — contains collapsed solutions in the day file itself; no separate answer key needed.

---

## Day 087 — Cluster Ops: Upgrades, Node Maintenance, etcd Backup, Pruning

**Warm-up:**
1. ⚠ General shape (per Day 86): `--endpoints`, `--cacert`, `--cert`, `--key` pointing at the control-plane's etcd certs.
2. Because the kubelet watches the static pod manifest directory directly and restarts the pod automatically whenever the manifest file changes — no `kubectl apply` is needed.
3. `kubectl auth can-i <verb> <resource> --as=<subject> -n <namespace>`.
4. Check node status → check static pod manifests on the affected node → check kubelet/container-runtime service status (`systemctl status kubelet`).

**Recall:**
1. Because skipping versions risks incompatible API changes — upgrades are supported only one minor version at a time (e.g. 1.29→1.30, never 1.29→1.31 directly).
2. `cordon` marks a node Unschedulable but leaves existing pods running; `drain` cordons AND gracefully evicts existing pods, moving them elsewhere.
3. Because DaemonSet pods are meant to run on every node and can't be "moved" — drain would otherwise refuse to proceed without this flag.
4. Because a backup stored on the same machine it protects can be lost in the same failure that destroys the original data — it must be copied off-node.
5. `revisionHistoryLimit` on the Deployment (default 10).
6. No — `uncordon` only allows new scheduling on the node again; it does not automatically move or rebalance existing pods back onto it.

---

## Day 088 — CRDs & Operators: When to Write vs Use

**Warm-up:**
1. ⚠ Not covered in this day's file — per Day 87, cluster upgrades must proceed one minor version at a time to avoid unsupported API skips.
2. `cordon`: existing pods keep running (only blocks new scheduling). `drain`: gracefully evicts existing pods after cordoning.
3. Because a backup on the same node it protects isn't a real backup — it must survive the failure of the node itself.
4. No — it does not automatically rebalance or move pods back.

**Recall:**
1. A CRD alone provides only a schema (a newly registered API kind) — it does nothing by itself; missing without a controller is the reconciliation loop that actually makes real infrastructure/state match the desired spec.
2. The CRD (the desired-state API) and a controller (the reconciliation loop that makes it real).
3. A standard `kubernetes.io/tls` Secret — it matters because Ingress (Day 66) already knows how to consume that exact Secret type for TLS termination, so no extra wiring is needed.
4. That cert-manager's controller was actively reconciling — it noticed the Secret's deletion and recreated it automatically within seconds, proving the reconciliation loop is live, not one-time.
5. Databases (Postgres/MongoDB operators), certificates (cert-manager), GitOps (ArgoCD) — service meshes (Istio) and monitoring (Prometheus Operator) are also named in the curriculum.
6. When the domain is genuinely novel to the org, no mature operator exists, and the ongoing operational cost of NOT automating it exceeds the cost of building/maintaining a custom controller.

---

## Day 089 — Phase 3 Mock Interview: k8s Architecture + Live Debugging

**Warm-up:**
1. A CRD alone provides only a schema/new API kind; missing without a controller is the reconciliation loop that acts on instances of it.
2. The CRD (desired-state API) plus a controller (the reconciliation loop).
3. That cert-manager's controller actively reconciles — it recreated the manually deleted Secret automatically.
4. When the domain is novel, no mature operator exists, and the ongoing cost of not automating exceeds building/maintaining one.

**Recall:**
1. `kubectl apply` → kube-apiserver validates and stores the Deployment in etcd → the Deployment controller creates a ReplicaSet → the ReplicaSet controller creates Pod objects → kube-scheduler assigns each Pod to a node → kubelet on that node pulls the image and starts the container via the container runtime (containerd/CRI-O); kube-proxy then makes it reachable through Service Endpoints.
2. Because ReplicaSet guarantees "N replicas running" (a reconciliation primitive) while Deployment adds rollout history and update strategy (versioned ReplicaSets) on top — splitting these lets Deployments manage revisions cleanly instead of one object doing both jobs.
3. Liveness failure → kubelet restarts the container. Readiness failure → kube-proxy removes the Pod from Service Endpoints, without restarting it.
4. RBAC restricting who can `get`/`list` Secrets, etcd encryption-at-rest, and external secret stores (Vault/sealed-secrets/sops) — base64 alone is not protection.
5. Pods lose their stable per-replica DNS identity (`pod-N.service` addressing) — clients can't reliably address a specific replica, breaking clustered/leader-election-dependent workloads.
6. A CRD (desired-state API) plus a controller (a reconciliation loop) that makes real state match it — e.g. cert-manager managing TLS certificates declaratively.

---

## Day 090 — ★ Phase 3 Capstone: Terraform-Provisioned Cluster, Helm-Deployed App

**Warm-up:**
1. App (Deployment), database (StatefulSet/Deployment+PVC), networking (Service + Ingress), and CronJob.
2. Terraform provisions infrastructure; Ansible/Helm configure what runs on/in it.
3. That cert-manager's controller was live and actively reconciling — it recreated a manually deleted Secret automatically.
4. Pod status → Events → container logs → exec into the container → check the dependency it relies on (the Day 73 troubleshooting funnel).

**Recall:**
1. Terraform (provisions the kind cluster) → Helm (`helm upgrade --install`, deploys app/db/ingress/cronjob onto it) — in that order, ideally driven by one CI pipeline.
2. Because it means Ingress works immediately on a freshly Terraform-created cluster with no manual cluster-recreation step needed — true "nothing to fully running app" reproducibility.
3. ⚠ Not a fixed value — it's whatever timing the learner records from their own destroy→apply→deploy cycle; there is no single correct number.
4. Because it documents WHY each tool was chosen at each layer for future readers, reviewers, and recruiters — working code alone doesn't communicate the reasoning behind an architecture.
5. Because publishing is an outward-facing, hard-to-reverse action — it should be drafted and held for the owner's explicit review and go-ahead before publishing.
6. ⚠ Open-ended — any concrete Day 73/85/86-style debugging story from the learner's own Phase 3 work (e.g. diagnosing a CrashLoopBackOff via Events/logs, or a DNS-failure hunt) is an appropriate answer.
