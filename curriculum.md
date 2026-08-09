# The 180-Day Curriculum Map

Every day, phase by phase. Click a day number to open its lesson.

**Structure:**
- Phase 0 (`day-0a`–`0e`) comes before Day 1 and assumes nothing at all.
- Every day divisible by 7 is a **review day**: no new material, a cumulative quiz,
  redo your weakest lab, and catch up.
- Days marked ★ are milestones for the incremental project at `/opt/fresher_project`,
  which you build from Day 1 and keep growing to the end.
- Certification anchors: RHCSA (Phase 1), CKA (Phase 3), AWS SAA and Terraform
  Associate (Phase 4). The curriculum is not built around passing exams, but it covers
  their ground.

---

## Phase 0 · Foundations (`day-0a` – `day-0e`)

Five gentle days that exist before Day 1, for anyone who has never really used a
computer beyond apps. Mostly hands-off, building the mental model first: what an OS
actually is, the terminal and shell, files and the filesystem, absolute versus relative
paths, then users, root and sudo. If you already work in a terminal, skim these and
start at Day 1.

| Day | Content |
|-----|---------|
| [0a](lessons/phase-0-foundations/day-0a.md) | What a computer and an operating system actually are |
| [0b](lessons/phase-0-foundations/day-0b.md) | Your first terminal: shell, commands, `man` |
| [0c](lessons/phase-0-foundations/day-0c.md) | Everything is a file: the Linux filesystem |
| [0d](lessons/phase-0-foundations/day-0d.md) | Absolute vs relative paths |
| [0e](lessons/phase-0-foundations/day-0e.md) | Users, root and sudo |


## Phase 1 · Linux, Networking, Bash, Python basics (Days 1–30)

| Day | Content |
|-----|---------|
| [1](lessons/phase-1-linux-networking-bash/day-001.md) | Linux architecture, boot process (BIOS/UEFI→GRUB→kernel→initramfs), systemd basics, journalctl. ★Project seed: `boot_logger.sh` logs boot info |
| [2](lessons/phase-1-linux-networking-bash/day-002.md) | Core CLI, FHS, file mgmt, I/O redirection, pipes |
| [3](lessons/phase-1-linux-networking-bash/day-003.md) | Users & groups, /etc/passwd\|shadow\|group, su vs sudo, sudoers, password policy. Project: create `svc_fresher` service user, script runs as it |
| [4](lessons/phase-1-linux-networking-bash/day-004.md) | Permissions deep: rwx/octal, umask, SUID/SGID/sticky, ACLs (getfacl/setfacl), chattr. Project: lock down /opt/fresher_project perms properly |
| [5](lessons/phase-1-linux-networking-bash/day-005.md) | Text processing I: grep -rEinv, regex fundamentals (anchors, classes, quantifiers) |
| [6](lessons/phase-1-linux-networking-bash/day-006.md) | Text processing II: sed (s///, -i, addresses), awk (fields, patterns), sort/uniq/cut/tr/paste. Project: boot_logger gains awk summary of log stats |
| [7](lessons/phase-1-linux-networking-bash/day-007.md) | REVIEW |
| [8](lessons/phase-1-linux-networking-bash/day-008.md) | Processes & signals: ps/top/htop, kill/signals, nice, jobs/fg/bg, /proc/<pid> deep, lsof |
| [9](lessons/phase-1-linux-networking-bash/day-009.md) | Package mgmt: dnf/rpm, repos, groups, history/rollback, modularity; rpm -q forensics |
| [10](lessons/phase-1-linux-networking-bash/day-010.md) | Storage I: lsblk/fdisk/parted, filesystems (xfs/ext4), mkfs, mount/umount, /etc/fstab, UUIDs |
| [11](lessons/phase-1-linux-networking-bash/day-011.md) | Storage II: LVM (pv/vg/lv, extend), swap, NFS client + autofs intro |
| [12](lessons/phase-1-linux-networking-bash/day-012.md) | systemd deep: unit anatomy, targets, dependencies, timers vs cron, WRITE a unit. ★Project: boot_logger becomes a systemd service + timer |
| [13](lessons/phase-1-linux-networking-bash/day-013.md) | Logging: journald config/persistence, rsyslog, logrotate; log triage method. Project: logrotate config replaces hand-rolled prune from D2 |
| [14](lessons/phase-1-linux-networking-bash/day-014.md) | REVIEW |
| [15](lessons/phase-1-linux-networking-bash/day-015.md) | Networking I: TCP/IP model, IP addressing, subnetting drills, MAC/ARP, ip a/ip r |
| [16](lessons/phase-1-linux-networking-bash/day-016.md) | Networking II: nmcli static config, routing, DNS client (resolv.conf, dig), /etc/hosts, ss |
| [17](lessons/phase-1-linux-networking-bash/day-017.md) | Networking III: SSH deep — keys, ssh-agent, sshd hardening, config file, tunnels, scp/rsync. Project: rsync-based backup of project dir to a second VM |
| [18](lessons/phase-1-linux-networking-bash/day-018.md) | firewalld: zones/services/ports/rich rules; intro SELinux modes & contexts |
| [19](lessons/phase-1-linux-networking-bash/day-019.md) | SELinux deep: booleans, fcontext/restorecon, audit2why; general troubleshooting method (top-down) |
| [20](lessons/phase-1-linux-networking-bash/day-020.md) | Web serving basics: install nginx, serve a page, curl -v anatomy, HTTP verbs/codes/headers, TLS concepts (self-signed cert). Project: serve boot_logger's HTML report via nginx |
| [21](lessons/phase-1-linux-networking-bash/day-021.md) | REVIEW |
| [22](lessons/phase-1-linux-networking-bash/day-022.md) | Bash I: variables, quoting rules, test/ , if/case, loops, read |
| [23](lessons/phase-1-linux-networking-bash/day-023.md) | Bash II: functions, positional args, exit codes, arrays, traps, here-docs |
| [24](lessons/phase-1-linux-networking-bash/day-024.md) | Bash III: robust scripts — set -euo pipefail, shellcheck, getopts, cron/at scheduling. ★Project: boot_logger refactored to robust-bash standard |
| [25](lessons/phase-1-linux-networking-bash/day-025.md) | Python I: install/venv, syntax, types, control flow, REPL habits |
| [26](lessons/phase-1-linux-networking-bash/day-026.md) | Python II: functions, files/context managers, exceptions, dicts/lists idioms |
| [27](lessons/phase-1-linux-networking-bash/day-027.md) | Python III: modules/pip, argparse, os/pathlib/subprocess — Python as a bash upgrade. Project: `sys_report.py` v0 (CPU/mem/disk snapshot → JSON) |
| [28](lessons/phase-1-linux-networking-bash/day-028.md) | REVIEW |
| [29](lessons/phase-1-linux-networking-bash/day-029.md) | RHCSA-style mock skills day: timed tasks across D1–27 (users, storage, systemd, SELinux, networking) |
| [30](lessons/phase-1-linux-networking-bash/day-030.md) | ★Phase capstone: `system_report` tool — bash collector + python formatter, systemd timer, nginx-served output, documented README |

## Phase 2 · Git, Python for Ops, CI/CD, Docker, Ansible (Days 31–60)

| Day | Content |
|-----|---------|
| [31](lessons/phase-2-git-cicd-docker-ansible/day-031.md) | Git I: init/add/commit/log/diff, the object model (blob/tree/commit), .git internals peek |
| [32](lessons/phase-2-git-cicd-docker-ansible/day-032.md) | Git II: branching/merging, conflicts (create + resolve on purpose), stash |
| [33](lessons/phase-2-git-cicd-docker-ansible/day-033.md) | Git III: remotes, GitHub account, push/pull/fetch, PR workflow, .gitignore, README/licenses. ★Project goes PUBLIC on GitHub — job-readiness starts |
| [34](lessons/phase-2-git-cicd-docker-ansible/day-034.md) | Git IV: rebase vs merge, interactive-rebase concepts, reset/reflog rescue, tags/releases, trunk-based vs gitflow |
| [35](lessons/phase-2-git-cicd-docker-ansible/day-035.md) | REVIEW |
| [36](lessons/phase-2-git-cicd-docker-ansible/day-036.md) | Python for Ops I: requests, REST APIs, JSON handling, error/retry patterns |
| [37](lessons/phase-2-git-cicd-docker-ansible/day-037.md) | Python for Ops II: building CLIs (argparse/click), logging module done right, config files |
| [38](lessons/phase-2-git-cicd-docker-ansible/day-038.md) | Python for Ops III: pytest basics, project layout, type hints intro. Project: tests for sys_report |
| [39](lessons/phase-2-git-cicd-docker-ansible/day-039.md) | Data formats day: YAML deep (anchors, gotchas), JSON/TOML, jq/yq mastery drills |
| [40](lessons/phase-2-git-cicd-docker-ansible/day-040.md) | CI/CD concepts: pipeline anatomy, artifacts, environments; GitHub Actions I — first workflow (lint+test on push) |
| [41](lessons/phase-2-git-cicd-docker-ansible/day-041.md) | GitHub Actions II: matrix builds, secrets, caching, badges. ★Project: full CI (shellcheck+pytest) green on GitHub |
| [42](lessons/phase-2-git-cicd-docker-ansible/day-042.md) | REVIEW |
| [43](lessons/phase-2-git-cicd-docker-ansible/day-043.md) | Jenkins I: install (container), UI, freestyle vs pipeline, first Jenkinsfile |
| [44](lessons/phase-2-git-cicd-docker-ansible/day-044.md) | Jenkins II: agents, credentials, parameters; GitLab CI compare (read-only tour) — transferable pipeline thinking |
| [45](lessons/phase-2-git-cicd-docker-ansible/day-045.md) | Docker I: images vs containers, run/exec/logs/inspect, lifecycle, registries/pull |
| [46](lessons/phase-2-git-cicd-docker-ansible/day-046.md) | Docker II: Dockerfile — layers/cache, multi-stage builds, ENTRYPOINT vs CMD. Project: containerize sys_report |
| [47](lessons/phase-2-git-cicd-docker-ansible/day-047.md) | Docker III: volumes, bind mounts, networks, docker compose v2 |
| [48](lessons/phase-2-git-cicd-docker-ansible/day-048.md) | Docker IV: image hygiene — non-root, healthcheck, .dockerignore, scan (trivy). ★Project: hardened image pushed to registry from CI |
| [49](lessons/phase-2-git-cicd-docker-ansible/day-049.md) | REVIEW |
| [50](lessons/phase-2-git-cicd-docker-ansible/day-050.md) | Compose a 2-service app (app + db) locally; env-based config; intro 12-factor |
| [51](lessons/phase-2-git-cicd-docker-ansible/day-051.md) | Ansible I: inventory, ad-hoc commands, modules, idempotency as the core idea |
| [52](lessons/phase-2-git-cicd-docker-ansible/day-052.md) | Ansible II: playbooks, variables/facts, handlers, Jinja2 templates |
| [53](lessons/phase-2-git-cicd-docker-ansible/day-053.md) | Ansible III: roles, directory layout, galaxy, ansible-vault |
| [54](lessons/phase-2-git-cicd-docker-ansible/day-054.md) | Ansible IV: error handling, tags, check mode; playbook that fully configures a fresh VM (users, pkgs, nginx, project) |
| [55](lessons/phase-2-git-cicd-docker-ansible/day-055.md) | Dev-tooling glue: Makefiles, pre-commit hooks, EditorConfig, semantic versioning + CHANGELOG |
| [56](lessons/phase-2-git-cicd-docker-ansible/day-056.md) | REVIEW |
| [57](lessons/phase-2-git-cicd-docker-ansible/day-057.md) | End-to-end thread: commit → GitHub Actions → build+scan image → Ansible deploys to VM (webhook or workflow dispatch) |
| [58](lessons/phase-2-git-cicd-docker-ansible/day-058.md) | Secrets management: env vars vs files, GitHub secrets patterns, sops/age intro, what NEVER goes in git (+history scrubbing awareness) |
| [59](lessons/phase-2-git-cicd-docker-ansible/day-059.md) | Phase 2 mock interview: git scenarios, CI design, docker debugging; whiteboard the project pipeline |
| [60](lessons/phase-2-git-cicd-docker-ansible/day-060.md) | ★Phase capstone: project = public repo, tested, containerized, CI-built, Ansible-deployed to a VM; write the pipeline README + first LinkedIn post |

## Phase 3 · Kubernetes, Helm, Kustomize, Terraform (Days 61–90)

| Day | Content |
|-----|---------|
| [61](lessons/phase-3-kubernetes-terraform/day-061.md) | Why orchestration; k8s architecture: control plane, kubelet, etcd, scheduler, controllers; declarative model |
| [62](lessons/phase-3-kubernetes-terraform/day-062.md) | Local cluster (kind), kubectl fluency (get/describe/logs/exec, -o yaml/jsonpath), pods deep (init, sidecars, lifecycle) |
| [63](lessons/phase-3-kubernetes-terraform/day-063.md) | REVIEW |
| [64](lessons/phase-3-kubernetes-terraform/day-064.md) | Deployments/ReplicaSets: rollouts, rollbacks, strategies, revision history |
| [65](lessons/phase-3-kubernetes-terraform/day-065.md) | Services & networking model: ClusterIP/NodePort/LoadBalancer, endpoints, DNS, kube-proxy; NetworkPolicy intro |
| [66](lessons/phase-3-kubernetes-terraform/day-066.md) | Ingress + controller (nginx-ingress on kind), host/path routing, TLS |
| [67](lessons/phase-3-kubernetes-terraform/day-067.md) | ConfigMaps & Secrets: envFrom, mounts, immutability, secret caveats. Project: sys_report app configured via CM |
| [68](lessons/phase-3-kubernetes-terraform/day-068.md) | Storage: PV/PVC/StorageClass, dynamic provisioning; StatefulSets (db for project) |
| [69](lessons/phase-3-kubernetes-terraform/day-069.md) | Health & resources: liveness/readiness/startup probes, requests/limits, QoS, HPA + metrics-server |
| [70](lessons/phase-3-kubernetes-terraform/day-070.md) | REVIEW |
| [71](lessons/phase-3-kubernetes-terraform/day-071.md) | RBAC: roles/bindings, ServiceAccounts, security contexts, pod security standards |
| [72](lessons/phase-3-kubernetes-terraform/day-072.md) | Scheduling: nodeSelector/affinity/anti-affinity, taints/tolerations, DaemonSets, Jobs/CronJobs. Project: report job → CronJob |
| [73](lessons/phase-3-kubernetes-terraform/day-073.md) | Troubleshooting day: events, crashloops, pending pods, DNS failures — a debugging playbook (break things on purpose) |
| [74](lessons/phase-3-kubernetes-terraform/day-074.md) | Helm I: consuming charts (values, upgrade/rollback), repo ecosystem |
| [75](lessons/phase-3-kubernetes-terraform/day-075.md) | Helm II: author a chart for the project — templates, values.yaml, helpers, lint/test |
| [76](lessons/phase-3-kubernetes-terraform/day-076.md) | Kustomize: bases/overlays, dev vs prod envs; Helm vs Kustomize decision framework |
| [77](lessons/phase-3-kubernetes-terraform/day-077.md) | REVIEW |
| [78](lessons/phase-3-kubernetes-terraform/day-078.md) | ★Project on k8s: full app (app+db+ingress+CronJob) via own Helm chart, deployed from CI to kind |
| [79](lessons/phase-3-kubernetes-terraform/day-079.md) | Terraform I: HCL, providers, init/plan/apply/destroy, state file anatomy |
| [80](lessons/phase-3-kubernetes-terraform/day-080.md) | Terraform II: variables/outputs/locals, modules (write one), data sources |
| [81](lessons/phase-3-kubernetes-terraform/day-081.md) | Terraform III: remote state + locking, workspaces, import, drift |
| [82](lessons/phase-3-kubernetes-terraform/day-082.md) | Terraform IV: practice against docker/kind providers locally; lifecycle meta-args, count/for_each |
| [83](lessons/phase-3-kubernetes-terraform/day-083.md) | IaC discipline: Terraform + Ansible boundaries (provision vs configure), fmt/validate/tflint in CI |
| [84](lessons/phase-3-kubernetes-terraform/day-084.md) | REVIEW |
| [85](lessons/phase-3-kubernetes-terraform/day-085.md) | CKA drill day I: timed cluster tasks (workloads, networking, storage) |
| [86](lessons/phase-3-kubernetes-terraform/day-086.md) | CKA drill day II: timed troubleshooting + RBAC + etcd backup/restore |
| [87](lessons/phase-3-kubernetes-terraform/day-087.md) | Cluster ops: upgrades (kubeadm concepts), node maintenance (cordon/drain), etcd backup, resource pruning |
| [88](lessons/phase-3-kubernetes-terraform/day-088.md) | CRDs & operators: what they are, install one (e.g. cert-manager), read its CRDs; when to write vs use |
| [89](lessons/phase-3-kubernetes-terraform/day-089.md) | Phase 3 mock interview: k8s architecture Qs + live debugging scenario |
| [90](lessons/phase-3-kubernetes-terraform/day-090.md) | ★Phase capstone: project fully on k8s via Helm; kind cluster itself stood up by Terraform; architecture diagram + post |

## Phase 4 · AWS deep, cloud mapping, FinOps (Days 91–120)

| Day | Content |
|-----|---------|
| [91](lessons/phase-4-aws-cloud-finops/day-091.md) | REVIEW (consolidate P3 before cloud) |
| [92](lessons/phase-4-aws-cloud-finops/day-092.md) | Cloud concepts (IaaS/PaaS/SaaS, regions/AZs, shared responsibility); AWS free-tier account, console tour, IAM user setup |
| [93](lessons/phase-4-aws-cloud-finops/day-093.md) | **Cost safety FIRST**: billing alerts, budgets, Cost Explorer tour; IAM deep — users/groups/roles/policies (JSON), MFA, CLI profiles |
| [94](lessons/phase-4-aws-cloud-finops/day-094.md) | EC2 I: instances, AMIs, instance types, SGs, keypairs, user-data |
| [95](lessons/phase-4-aws-cloud-finops/day-095.md) | EC2 II: EBS/snapshots, launch templates, ASG, ALB/target groups |
| [96](lessons/phase-4-aws-cloud-finops/day-096.md) | VPC I: CIDR planning, subnets, route tables, IGW, NAT |
| [97](lessons/phase-4-aws-cloud-finops/day-097.md) | VPC II: NACLs vs SGs, VPC endpoints, peering; bastion + private-subnet pattern (build it) |
| [98](lessons/phase-4-aws-cloud-finops/day-098.md) | REVIEW |
| [99](lessons/phase-4-aws-cloud-finops/day-099.md) | S3: buckets/classes/lifecycle, versioning, encryption, presigned URLs, static site |
| [100](lessons/phase-4-aws-cloud-finops/day-100.md) | Databases: RDS (provision, connect from private subnet, snapshots) + DynamoDB basics |
| [101](lessons/phase-4-aws-cloud-finops/day-101.md) | Edge & DNS: Route 53, CloudFront, ACM certs; custom domain on the static site |
| [102](lessons/phase-4-aws-cloud-finops/day-102.md) | Serverless: Lambda (+ triggers), API Gateway, EventBridge, SQS/SNS overview |
| [103](lessons/phase-4-aws-cloud-finops/day-103.md) | Containers on AWS: ECR push from CI; ECS/Fargate deploy of project image |
| [104](lessons/phase-4-aws-cloud-finops/day-104.md) | EKS: managed k8s, node groups, IRSA concept; connect P3 skills — deploy Helm chart to EKS |
| [105](lessons/phase-4-aws-cloud-finops/day-105.md) | REVIEW |
| [106](lessons/phase-4-aws-cloud-finops/day-106.md) | Observability on AWS: CloudWatch metrics/logs/alarms/dashboards, CloudTrail, Config intro |
| [107](lessons/phase-4-aws-cloud-finops/day-107.md) | Terraform on AWS I: VPC + EC2 + SG stack from scratch, remote state in S3+lock |
| [108](lessons/phase-4-aws-cloud-finops/day-108.md) | Terraform on AWS II: ★project infra as a TF module set (network, cluster/ECS, db, bucket) |
| [109](lessons/phase-4-aws-cloud-finops/day-109.md) | Security & Well-Architected: least-privilege audit of everything built, trusted advisor, WAR review of project |
| [110](lessons/phase-4-aws-cloud-finops/day-110.md) | ★End-to-end: GitHub Actions → ECR → EKS/ECS deploy of project, DNS + TLS, all via Terraform |
| [111](lessons/phase-4-aws-cloud-finops/day-111.md) | AWS SAA drill day + **resume checkpoint #1** (cloud skills added) |
| [112](lessons/phase-4-aws-cloud-finops/day-112.md) | REVIEW |
| [113](lessons/phase-4-aws-cloud-finops/day-113.md) | Azure mapping I: subscriptions/RG, VNet vs VPC, Entra vs IAM, VM/storage equivalents — concept mapping table |
| [114](lessons/phase-4-aws-cloud-finops/day-114.md) | Azure mapping II: AKS quick deploy of the Helm chart; Azure DevOps glance; "one cloud deep, others mapped" thesis |
| [115](lessons/phase-4-aws-cloud-finops/day-115.md) | GCP mapping: projects, IAM diffs, GKE (deploy chart), BigQuery mention; when-which-cloud decision framework |
| [116](lessons/phase-4-aws-cloud-finops/day-116.md) | Multi-cloud reality: portability myths, egress economics, abstraction costs; hybrid patterns |
| [117](lessons/phase-4-aws-cloud-finops/day-117.md) | FinOps I (owner's specialty): cost model, tagging strategy, allocation, unit economics, showback/chargeback |
| [118](lessons/phase-4-aws-cloud-finops/day-118.md) | FinOps II: rightsizing, RI/Savings Plans/Spot, storage tiering, k8s cost (Kubecost on the cluster) |
| [119](lessons/phase-4-aws-cloud-finops/day-119.md) | REVIEW |
| [120](lessons/phase-4-aws-cloud-finops/day-120.md) | ★Phase capstone: project on AWS cost-optimized; produce a FinOps report (spend, tags, savings applied) + Terraform Associate/SAA readiness check |

## Phase 5 · SRE, Observability, GitOps, Mesh, Chaos, Platform (Days 121–150)

| Day | Content |
|-----|---------|
| [121](lessons/phase-5-sre-observability-gitops/day-121.md) | SRE principles: SLI/SLO/SLA, error budgets, toil, on-call philosophy; draft SLOs for the project |
| [122](lessons/phase-5-sre-observability-gitops/day-122.md) | Prometheus I: architecture, exporters (node_exporter), scrape config, PromQL basics |
| [123](lessons/phase-5-sre-observability-gitops/day-123.md) | Prometheus II: PromQL deep (rate/increase/histogram_quantile), recording + alerting rules |
| [124](lessons/phase-5-sre-observability-gitops/day-124.md) | Grafana: build the project dashboard (USE/RED methods), variables, alerting |
| [125](lessons/phase-5-sre-observability-gitops/day-125.md) | Alertmanager: routing/silencing/grouping; runbooks — write one per alert |
| [126](lessons/phase-5-sre-observability-gitops/day-126.md) | REVIEW |
| [127](lessons/phase-5-sre-observability-gitops/day-127.md) | Logging stack I: EFK vs Loki tradeoffs; deploy Loki+promtail (lighter) on cluster |
| [128](lessons/phase-5-sre-observability-gitops/day-128.md) | Logging II: parsing/labels, LogQL, retention; structured logging in the app |
| [129](lessons/phase-5-sre-observability-gitops/day-129.md) | Tracing: OpenTelemetry concepts, instrument the project, Jaeger UI, trace-log-metric correlation |
| [130](lessons/phase-5-sre-observability-gitops/day-130.md) | ★Project fully instrumented: metrics+logs+traces, SLO dashboards live, burn-rate alerts |
| [131](lessons/phase-5-sre-observability-gitops/day-131.md) | Incident mgmt: sev levels, comms, postmortem template; run a tabletop incident |
| [132](lessons/phase-5-sre-observability-gitops/day-132.md) | GitOps I: pull vs push, ArgoCD install, first app, app-of-apps pattern |
| [133](lessons/phase-5-sre-observability-gitops/day-133.md) | REVIEW |
| [134](lessons/phase-5-sre-observability-gitops/day-134.md) | GitOps II: ★project managed by ArgoCD (sync policies, auto-prune, rollback demo); image-updater concept |
| [135](lessons/phase-5-sre-observability-gitops/day-135.md) | Service mesh I: Istio concepts — sidecars, virtual services, traffic splitting |
| [136](lessons/phase-5-sre-observability-gitops/day-136.md) | Istio II: mTLS, canary release of the project, mesh observability (Kiali) |
| [137](lessons/phase-5-sre-observability-gitops/day-137.md) | Chaos engineering: principles, blast radius; run pod-kill/network-delay experiments (chaos-mesh/Litmus) against SLOs |
| [138](lessons/phase-5-sre-observability-gitops/day-138.md) | Performance & load: k6 load tests, find the saturation point, capacity math |
| [139](lessons/phase-5-sre-observability-gitops/day-139.md) | Advanced cluster ops: cluster-autoscaler/Karpenter concepts, PDBs, cost+reliability balance (FinOps tie-back) |
| [140](lessons/phase-5-sre-observability-gitops/day-140.md) | REVIEW |
| [141](lessons/phase-5-sre-observability-gitops/day-141.md) | Platform engineering: IDP concept, golden paths; Backstage install + catalog the project |
| [142](lessons/phase-5-sre-observability-gitops/day-142.md) | Backstage II: software template (scaffold a new service in 5 min), TechDocs |
| [143](lessons/phase-5-sre-observability-gitops/day-143.md) | DevSecOps: SAST/dep scanning in CI (trivy/grype), SBOM, OPA/Gatekeeper policy on cluster |
| [144](lessons/phase-5-sre-observability-gitops/day-144.md) | Reliability patterns: timeouts/retries/backoff, circuit breakers, graceful degradation, health-check design |
| [145](lessons/phase-5-sre-observability-gitops/day-145.md) | Game day: full live incident simulation (instructor breaks prod, learner on-call, postmortem after) |
| [146](lessons/phase-5-sre-observability-gitops/day-146.md) | SRE interview prep: troubleshooting drills, "design monitoring for X", SLO math questions |
| [147](lessons/phase-5-sre-observability-gitops/day-147.md) | REVIEW |
| [148](lessons/phase-5-sre-observability-gitops/day-148.md) | **Resume checkpoint #2** + portfolio polish: README audit, architecture diagrams, pin repos, LinkedIn rewrite |
| [149](lessons/phase-5-sre-observability-gitops/day-149.md) | Mock interviews (DevOps + SRE loops), feedback, gap list |
| [150](lessons/phase-5-sre-observability-gitops/day-150.md) | ★Phase capstone: project = GitOps-managed, meshed, SLO'd, chaos-tested platform; write the "how I built it" long-form post |

## Phase 6 · MLOps, LLMOps, AIOps, Capstone (Days 151–180)

| Day | Content |
|-----|---------|
| [151](lessons/phase-6-mlops-llmops-aiops/day-151.md) | ML for ops engineers: lifecycle, train/serve split, where DevOps skills map; scikit-learn hello-model |
| [152](lessons/phase-6-mlops-llmops-aiops/day-152.md) | MLflow: tracking, params/metrics/artifacts, model registry; track a small model on project metrics data |
| [153](lessons/phase-6-mlops-llmops-aiops/day-153.md) | DVC: data versioning, pipelines, remotes; reproducibility discipline |
| [154](lessons/phase-6-mlops-llmops-aiops/day-154.md) | REVIEW |
| [155](lessons/phase-6-mlops-llmops-aiops/day-155.md) | Model serving: FastAPI wrapper, containerize, deploy to cluster with the existing Helm/CI muscle |
| [156](lessons/phase-6-mlops-llmops-aiops/day-156.md) | Kubeflow/managed-MLOps overview: pipelines concept, when platform vs DIY; SageMaker glance |
| [157](lessons/phase-6-mlops-llmops-aiops/day-157.md) | ML CI/CD: retraining pipeline, model gates/tests, promotion flow (registry→prod) |
| [158](lessons/phase-6-mlops-llmops-aiops/day-158.md) | Model monitoring: drift concepts, performance tracking, feedback loops — reuse P5 observability |
| [159](lessons/phase-6-mlops-llmops-aiops/day-159.md) | LLM fundamentals for ops: tokens/context, inference vs training, GPU basics, latency/cost levers |
| [160](lessons/phase-6-mlops-llmops-aiops/day-160.md) | LLMOps I: serve a small OSS model with vLLM (CPU-friendly alt if no GPU), OpenAI-compatible APIs |
| [161](lessons/phase-6-mlops-llmops-aiops/day-161.md) | REVIEW |
| [162](lessons/phase-6-mlops-llmops-aiops/day-162.md) | LLMOps II: RAG architecture — embeddings, vector DB (qdrant/chroma), chunking; build minimal RAG over project docs |
| [163](lessons/phase-6-mlops-llmops-aiops/day-163.md) | LLMOps III: agent/tool-use concepts, guardrails, eval basics (golden sets); LangChain minimal use |
| [164](lessons/phase-6-mlops-llmops-aiops/day-164.md) | LLM cost/perf ops: caching, batching, quantization, GPU utilization — FinOps for AI (differentiator) |
| [165](lessons/phase-6-mlops-llmops-aiops/day-165.md) | AIOps I: ★anomaly detection on the project's Prometheus metrics (simple model, alert on anomalies) — the project's ML component |
| [166](lessons/phase-6-mlops-llmops-aiops/day-166.md) | AIOps II: log clustering / alert-noise reduction concepts; wire anomaly alerts into Alertmanager |
| [167](lessons/phase-6-mlops-llmops-aiops/day-167.md) | Capstone spec day: scope, architecture review with owner, success criteria, 8-day build plan |
| [168](lessons/phase-6-mlops-llmops-aiops/day-168.md) | REVIEW |
| [169](lessons/phase-6-mlops-llmops-aiops/day-169.md) | Capstone build 1: repo scaffold (via Backstage template), infra (Terraform) |
| [170](lessons/phase-6-mlops-llmops-aiops/day-170.md) | Capstone build 2: core services + CI |
| [171](lessons/phase-6-mlops-llmops-aiops/day-171.md) | Capstone build 3: k8s/Helm deploy + GitOps |
| [172](lessons/phase-6-mlops-llmops-aiops/day-172.md) | Capstone build 4: observability + SLOs |
| [173](lessons/phase-6-mlops-llmops-aiops/day-173.md) | Capstone build 5: security + policy + secrets |
| [174](lessons/phase-6-mlops-llmops-aiops/day-174.md) | Capstone build 6: ML/AIOps component integration |
| [175](lessons/phase-6-mlops-llmops-aiops/day-175.md) | REVIEW (+ capstone buffer) |
| [176](lessons/phase-6-mlops-llmops-aiops/day-176.md) | Capstone build 7: chaos test + load test + hardening |
| [177](lessons/phase-6-mlops-llmops-aiops/day-177.md) | Capstone docs + record the demo video (OBS skills → publish it yourself) |
| [178](lessons/phase-6-mlops-llmops-aiops/day-178.md) | Job sprint: final resume, LinkedIn, 20 applications, referral asks |
| [179](lessons/phase-6-mlops-llmops-aiops/day-179.md) | Final mock-interview marathon (technical + HR + salary basics) |
| [180](lessons/phase-6-mlops-llmops-aiops/day-180.md) | Graduation: capstone presentation, full retrospective, 90-day job-hunt plan, what-to-learn-next map |

---

## After Day 180

The capstone is the artifact you show people. The 90-day job-hunt plan on Day 180 is
the part most courses leave out, and it is the part that turns 180 days of work into a
job.

Keep the project repo public and keep committing to it. A year of visible, incremental
work on one real system is worth more than any certificate on the pile.

