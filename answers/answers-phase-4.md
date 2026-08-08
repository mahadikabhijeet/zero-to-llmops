# Answer Key — Phase 4 (Days 91–120)

Trainer prep companion. Answers are grounded in each day's theory section; ⚠ marks
answers drawn from general knowledge beyond the day file — verify against official docs
before teaching.

---

## Day 091 — REVIEW (Phase 3 consolidation before cloud)

**Cumulative quiz (Days 84–90, outside Phase 4 file range — all ⚠, verify against Days 84–90 files):**
1. A rollout is a normal Deployment update (new ReplicaSet scaled up, old scaled down),
   triggered by a spec/image change and tracked with `kubectl rollout status`; a rollback
   reverts to a prior ReplicaSet revision via `kubectl rollout undo`. ⚠
2. ClusterIP (default, internal-only — service-to-service), NodePort (static port on
   every node's IP — quick external/dev access), LoadBalancer (provisions a cloud LB —
   production external access). ⚠
3. Ingress adds L7 (HTTP/HTTPS) host/path-based routing to multiple backend Services
   under one external address, plus TLS termination — a plain Service is L4 and 1:1 with
   its backend pods. ⚠
4. PV = the actual storage resource; PVC = a user's request/claim for storage matching a
   PV; StorageClass = a template letting a PVC dynamically provision a matching PV. ⚠
5. Liveness protects against a hung/deadlocked container (restarts it); readiness
   protects against routing traffic to a not-yet-ready pod (pulls it from Service
   endpoints); startup protects slow-starting containers from being killed by liveness
   before they're up. ⚠
6. RoleBinding grants a Role/ClusterRole's permissions within one namespace;
   ClusterRoleBinding grants a ClusterRole's permissions cluster-wide across all
   namespaces. ⚠
7. A taint repels pods from a node by default; a toleration on a pod only permits
   (doesn't force) scheduling there. Taint is the one that repels. ⚠
8. `helm upgrade --install` installs fresh if the release doesn't exist or upgrades it if
   it does (idempotent); plain `helm install` fails if already installed. ⚠
9. Remote state + locking gives a team one shared source of truth for infra state and
   prevents concurrent applies from corrupting it via a race condition. ⚠
10. Terraform provisions/manages infrastructure itself (declarative); Ansible configures
    software on hosts that already exist (procedural) — "Terraform builds it, Ansible
    configures it." ⚠

---

## Day 092 — Cloud Concepts, AWS Free-Tier Account, Console Tour, First IAM User

**Warm-up (from Days 89–90, outside file range — ⚠):**
1. The capstone ran the project's app+db+Ingress stack (via its own Helm chart) on a kind
   cluster stood up by Terraform. ⚠ verify exact wording against Day 90.
2. `terraform plan` shows the full diff of what would be created/changed/destroyed
   without making any change — lets you review before `apply` commits it.
3. Host/path-based L7 routing to multiple backend Services under one external endpoint
   (plus TLS termination) — a plain Service can't do this.
4. A taint repels pods from a node by default; a toleration on a pod only permits
   (doesn't force) scheduling there.

**Recall:**
1. Root has unrestrictable, full account access (billing, account closure) that can't be
   scoped by IAM policy — reserve it strictly for account-level tasks.
2. It confirmed the active IAM identity's ARN — proof the CLI was acting as
   `fresher-admin`, not root.
3. Most AWS services are regional — a resource created in one region is invisible from
   another.
4. Users, groups, roles, policies.
5. A named profile keeps credential sets explicit/isolated, preventing accidental use of
   the wrong identity and avoiding hardcoded keys in code.

---

## Day 093 — Cost Safety First (Billing Alerts, Budgets), IAM Deep

**Warm-up (Day 92):**
1. Root is the unrestrictable account owner (account-level tasks only); an IAM user is a
   scoped identity for day-to-day work.
2. It confirmed the caller's IAM identity (ARN), not root.
3. Most resources are regional — wrong-region creation makes a resource invisible.
4. Users, groups, roles, policies.

**Recall:**
1. AWS doesn't stop spend at $0 — budgets/alerts are the only proactive guardrail before
   the first billable resource exists.
2. Explicit Deny always wins over explicit Allow.
3. A role has no long-term credentials (assumed via `sts:AssumeRole` for temporary
   creds); a user has persistent credentials tied to one identity.
4. It checks `aws:MultiFactorAuthPresent`; combined with `BoolIfExists: "false"` + Deny,
   it denies actions when MFA isn't present on the session.
5. `~/.aws/credentials` holds secrets; `~/.aws/config` holds region/output/role config —
   neither belongs in git because credentials leak keys and both reveal account/role
   structure.

---

## Day 094 — EC2 I: Instances, AMIs, Instance Types, Security Groups, Keypairs, User-Data

**Warm-up (Day 93):**
1. Billing metrics publish only in `us-east-1` — alerts must be enabled there regardless
   of working region.
2. Explicit Deny.
3. `aws sts get-caller-identity`.
4. Every lab from Day 94 on ends with an explicit, mandatory teardown step.

**Recall:**
1. Stopped: no compute billing, still bills attached EBS. Terminated: bills for neither,
   cannot be restarted.
2. Default SG denies all inbound (must be explicitly opened) but allows all outbound —
   SGs are allow-only, outbound assumed safe until scoped.
3. User-data runs once, at first boot, via cloud-init — the bootstrapping hook.
4. IMDSv2 requires a session token, closing the SSRF vulnerability IMDSv1 has (any
   unauthenticated request could fetch metadata/credentials).
5. Terminate the instance → delete the SG → delete the keypair; the SG can't be deleted
   while still attached to an existing instance.

---

## Day 095 — EC2 II: EBS/Snapshots, Launch Templates, ASG, ALB/Target Groups

**Warm-up (Day 94):**
1. Stopped keeps EBS billing (no compute); terminated bills nothing and can't restart.
2. Default SG: deny all inbound, allow all outbound.
3. User-data runs once at first boot via cloud-init.
4. Mandatory explicit teardown of every created resource.

**Recall:**
1. A version of the full `run-instances` parameter set (AMI, type, SG, user-data, key),
   mutable/versioned, so an ASG can reference `$Latest` instead of hardcoding flags.
2. ELB health check is stricter — it requires the load balancer's own health check
   (app-level) to pass, not just instance-up; required once an ALB is attached.
3. ALB = L7 (HTTP/HTTPS, host/path routing); NLB = L4 (TCP/UDP raw forwarding).
4. ASG must be deleted first because it actively registers/deregisters instances into the
   target group — deleting the TG/ALB first leaves it referencing gone resources.
5. Terminating one instance manually and watching the ASG launch a replacement
   automatically (`describe-auto-scaling-groups`) proved self-healing.

---

## Day 096 — VPC I: CIDR Planning, Subnets, Route Tables, IGW, NAT

**Warm-up (Day 95):**
1. A launch template, min/max/desired capacity, and subnets across AZs.
2. EC2 health check only checks the instance is running; ELB health check checks the
   app-level health check passes — stricter.
3. The instance-id in repeated `curl` responses to the ALB changing across requests.
4. ASG → ALB → target group (delete order).

**Recall:**
1. A route table property (default route to an IGW) — not an inherent subnet attribute.
2. For the network address, VPC router, DNS, future use, and a broadcast-analog address —
   leaving a `/24` with 251 usable addresses.
3. It bills per-hour and per-GB processed, and is easy to leave running/oversized in a
   learning account — the single most common surprise bill.
4. Because the NAT Gateway keeps billing until fully deleted; releasing the EIP first
   could orphan/complicate the deletion.
5. The IGW attaches to the VPC as a whole (highly available, horizontally scaled by
   AWS) — not to any specific subnet; subnets become "public" via their route table.

---

## Day 097 — VPC II: NACLs vs SGs, VPC Endpoints, Peering, Bastion Pattern

**Warm-up (Day 96):**
1. A route table property (default route to an IGW).
2. Outbound-only internet access for updates; it did not allow inbound reachability from
   the internet.
3. Because the NAT Gateway keeps billing until fully deleted.
4. Public: `10.0.1.0/24` (AZ-a), `10.0.2.0/24` (AZ-b); Private: `10.0.11.0/24` (AZ-a),
   `10.0.12.0/24` (AZ-b).

**Recall:**
1. NACL is stateless (subnet-level; both directions, including ephemeral ports, must be
   explicitly allowed); SG is stateful (instance/ENI-level; return traffic auto-allowed).
2. It's a strict 1:1 connection — if A↔B and B↔C are peered, A cannot reach C through B;
   each pair needing connectivity must be peered directly (or use Transit Gateway).
3. A gateway endpoint (S3/DynamoDB) is free, route-table-based, no ENI; an interface
   endpoint is billed per-hour + per-GB and creates a PrivateLink-backed ENI.
4. It stays correct if the bastion's IP changes and scopes access to instances carrying
   that SG, not any host at a given address.
5. AWS Systems Manager Session Manager — removes the bastion host and the open SSH port
   entirely, using IAM-based access instead.

---

## Day 098 — REVIEW (Cloud fundamentals, IAM, EC2, VPC)

**Cumulative quiz (Days 92–97):**
1. AWS secures "of" the cloud for all three; EC2 additionally requires you to manage OS
   patching/data/network config, S3 shifts more of the stack to AWS (you manage data/
   access policies), Lambda shifts the runtime to AWS too (you manage code/config).
2. Because root's access can't be scoped by IAM policy — reserve it for account-level
   tasks (billing, account closure, first IAM setup).
3. AWS doesn't stop spend automatically; billing metrics publish only in `us-east-1`.
4. Explicit Deny always wins.
5. Stopped still bills attached EBS; terminated bills nothing.
6. A launch template, min/max/desired capacity, and subnets spread across AZs.
7. A route table property (default route to an IGW) — not a subnet attribute.
8. NAT Gateway bills per-hour + per-GB and is easy to oversize/forget; a free S3/DynamoDB
   gateway endpoint removes that cost for that traffic.
9. NACL is stateless (must allow both directions incl. ephemeral ports); SG is stateful
   (return traffic auto-allowed) — forgetting the ephemeral-port rule breaks return
   traffic on a NACL.
10. It's a strict 1:1 connection — non-transitive by design.

---

## Day 099 — S3: Buckets, Storage Classes, Lifecycle, Versioning, Encryption, Presigned URLs

**Warm-up (Days 92–97):**
1. AWS manages S3's infrastructure entirely; the customer manages data, access/bucket
   policies, and encryption configuration.
2. An S3 gateway endpoint (free, route-table-based).
3. It made the bastion the only path into private instances, using SSH agent forwarding
   so keys never touch the bastion.
4. Its EBS volume(s) can outlive the terminated compute record if not set to
   delete-on-termination or explicitly removed; any AMI/snapshot taken from it also
   survives regardless.

**Recall:**
1. Versioning turns deletes into delete markers instead of permanent removal, preventing
   accidental overwrite/delete data loss.
2. Block Public Access → bucket policy → IAM policy — all must permit.
3. Automating storage-class transitions and expiration on a schedule, cutting storage
   cost for aging data — the primary S3 FinOps lever.
4. A presigned URL grants temporary access to a private object without making it public
   or sharing credentials; the biggest gotcha is needing to remove every object *version*
   (and delete markers), not just the current one, to fully empty a versioned bucket.
5. Because S3 website endpoints serve HTTP only, with no TLS support; CloudFront fixes
   that by fronting the bucket with HTTPS.

---

## Day 100 — Databases: RDS (Private-Subnet Provisioning) + DynamoDB Basics

**Warm-up (Day 99):**
1. Versioning turns deletes into delete markers instead of removing the underlying data.
2. Block Public Access, bucket policy, and IAM policy must all agree.
3. It automated storage-class transitions/expiration, cutting storage cost for aging
   data.
4. Having to delete every object version (and delete markers), not just the current one.

**Recall:**
1. Because the DB Subnet Group is a hard prerequisite for a future Multi-AZ upgrade, even
   though today's instance is single-AZ.
2. Always a new instance/endpoint — restore is never in-place.
3. It should never be internet-facing; the replacement pattern is connecting only from
   the app tier or a bastion via SG-to-SG reference.
4. `query` (uses a key condition, efficient) is preferred over `scan` (reads the whole
   table, expensive).
5. On-demand suits unpredictable/spiky or learning workloads; provisioned is cheaper at
   steady, predictable high volume.

---

## Day 101 — Edge & DNS: Route 53, CloudFront, ACM, Custom Domain on the Static Site

**Warm-up (Day 100):**
1. The DB Subnet Group requires ≥2 AZs as a prerequisite for a future Multi-AZ upgrade.
2. A new instance/endpoint, never in-place.
3. `query` — uses a key condition, efficient; `scan` reads the whole table.
4. SG-to-SG reference, connecting only from the app tier or a bastion.

**Recall:**
1. Because CloudFront-bound ACM certs must be requested in `us-east-1` regardless of
   where other resources live.
2. It keeps the S3 origin bucket fully private (Block Public Access on) while letting
   only CloudFront read it — a public bucket policy would break that privacy.
3. Alias can point at AWS resources (CloudFront, ALB, S3 website) at the zone apex, which
   CNAME cannot do, at no extra query cost.
4. AWS requires a distribution to be disabled and reach `Deployed` state before it can be
   deleted.
5. Versioned filenames — cheaper than paying for cache invalidations at volume.

---

## Day 102 — Serverless: Lambda + Triggers, API Gateway, EventBridge, SQS/SNS

**Warm-up (Day 101):**
1. Because ACM certs for CloudFront must be requested in `us-east-1` specifically.
2. It keeps the origin bucket fully private while letting only CloudFront read it.
3. Alias can target AWS resources at the zone apex; CNAME can't exist at the apex.
4. AWS requires disabling (and `Deployed` state) before deletion is allowed.

**Recall:**
1. Push: API Gateway invoking Lambda directly; poll: SQS polled by Lambda's event source
   mapping.
2. The execution role governs what the function can call; S3 needs a separate
   resource-based `add-permission` grant so S3 itself is allowed to invoke the function —
   a different direction of trust.
3. HTTP API — newer, cheaper, simpler; the default unless REST API's advanced features
   (usage plans, request validation, private endpoints) are needed.
4. It captures repeatedly-failing ("poison") messages instead of blocking/looping the
   queue indefinitely.
5. Broadcasting one event to multiple independent consumers (SNS topic → multiple SQS
   queues, one per consumer).

---

## Day 103 — Containers on AWS: ECR from CI, ECS/Fargate Deploy of the Project

**Warm-up (Day 102):**
1. Push: API Gateway → Lambda; poll: SQS → Lambda event source mapping.
2. Because the IAM execution role controls what the function can call, but S3 needs a
   separate resource-based permission to be allowed to invoke it.
3. It captures messages that repeatedly fail processing instead of blocking the queue.
4. Broadcasting one event to multiple independent consumers.

**Recall:**
1. Execution role: AWS-facing actions (pull image, write logs). Task role: app-facing
   actions (what the running app itself can call, e.g. S3/DynamoDB).
2. `awsvpc` gives each task its own ENI and private IP in the VPC, so SGs can attach
   directly to the task, same model as an EC2 instance.
3. Task definitions are immutable — every deploy registers a new revision, so rollback is
   just pointing the service back at a prior revision number.
4. It redeploys the service's tasks under the same (or newly registered) task definition
   without changing desired count.
5. OIDC avoids storing long-lived AWS keys in GitHub secrets — credentials are short-lived
   and scoped per run, reducing leak blast radius.

---

## Day 104 — EKS: Managed Kubernetes, Node Groups, IRSA; Deploy the Project's Helm Chart

**Warm-up (Day 103):**
1. Execution role: image pull/logs (AWS-facing); task role: app's own AWS calls
   (app-facing).
2. It gives each task its own ENI/private IP so SGs attach per-task, same model as EC2.
3. Because they're immutable — every deploy is a new revision, so rollback just repoints
   the service.
4. It avoids storing long-lived AWS keys in GitHub secrets.

**Recall:**
1. Structurally different: EKS's control plane (API server, etcd, scheduler) is
   AWS-managed; you bring/manage the worker-node data plane. Identical: the Kubernetes
   API and `kubectl` — the Helm chart doesn't change.
2. IRSA scopes AWS credentials to a specific pod's ServiceAccount; an EC2 instance
   profile only scopes credentials to the whole node, over-granting every pod on it.
3. Because worker nodes depend on the cluster's control plane/networking — deleting the
   cluster first would orphan the node group's resources.
4. The AWS Load Balancer Controller (Ingress/LoadBalancer → real ALBs/NLBs) and the EBS
   CSI driver (PVCs backed by real EBS volumes).
5. EKS node groups are an always-on EC2 fleet billing continuously even when idle;
   Fargate-based ECS has no persistent node fleet, so forgotten EKS node groups are a
   bigger silent-cost risk.

---

## Day 105 — REVIEW (S3, RDS/DynamoDB, DNS/CDN, Serverless, ECS, EKS)

**Cumulative quiz (Days 99–104):**
1. Enabling versioning turns deletes into delete markers instead of permanently removing
   data.
2. The DB Subnet Group requires it as a prerequisite for a future Multi-AZ upgrade.
3. Block Public Access → bucket policy → IAM policy, all must agree.
4. CloudFront-bound ACM certs must be requested in `us-east-1` regardless of other
   resources' region.
5. Push: API Gateway → Lambda; poll: SQS → Lambda event source mapping.
6. Execution role: AWS-facing (image pull/logs); task role: app-facing (what the app can
   call).
7. Task definitions are immutable — every deploy is a new revision, enabling instant
   rollback.
8. EKS's control plane is AWS-managed (data plane/nodes are yours); identical: the
   Kubernetes API and `kubectl`/Helm workflow.
9. IRSA scopes AWS credentials to a specific pod (via its ServiceAccount) rather than the
   whole node.
10. Automating storage-class transitions/expiration to cut storage cost for aging data.

---

## Day 106 — Observability on AWS: CloudWatch, CloudTrail, Config Intro

**Warm-up (Days 99–104):**
1. Execution role: image pull/logs; task role: app's own AWS calls.
2. Scoping AWS permissions to a specific pod via its ServiceAccount instead of the whole
   node.
3. The DB Subnet Group requires ≥2 AZs as a prerequisite for a future Multi-AZ upgrade.
4. Automating storage-class transitions/expiration to cut cost for aging data.

**Recall:**
1. An average smooths out spikes; p99 surfaces the worst-experienced (tail) latency that
   an average completely hides.
2. Infinite (no expiration) by default — an easy forgotten-cost trap.
3. CloudTrail answers who called an API, when, and from where; CloudWatch Logs only
   captures application-level log output.
4. CloudTrail tracks who-did-what (API call history); AWS Config tracks resource
   *configuration state* over time against compliance rules.
5. JSON makes a dashboard reviewable, diffable, and repeatable (infra-as-data) instead of
   manually reassembled by hand each time.

---

## Day 107 — Terraform on AWS I: VPC + EC2 + SG from Scratch, Remote State in S3 + Lock

**Warm-up (Day 106, plus a Days 79–83 recall item — ⚠ outside file range):**
1. An average smooths out spikes that a p99 metric would surface as tail latency pain.
2. Infinite (no expiration) by default.
3. CloudTrail answers who made an API call and when; Logs only capture app-level output.
4. `terraform plan` shows the diff before committing; remote state solves sharing one
   authoritative state file across a team/CI instead of divergent local copies. ⚠

**Recall:**
1. On first run there's no backend yet to store state in — the state bucket must be
   bootstrapped manually, outside Terraform (a known chicken-and-egg exception).
2. It prevents two concurrent `apply` runs from writing to the same state simultaneously
   and corrupting it via a race condition.
3. Because state can contain plaintext secrets (e.g. DB passwords) — encryption and
   keeping it out of git protect those secrets.
4. It manually releases a stuck lock (e.g. crashed process) — not routine, because misuse
   risks concurrent-write corruption.
5. Running `fmt`/`validate`/`tflint` before every commit — unchanged against a real cloud
   target.

---

## Day 108 — Terraform on AWS II: ★Project Infra as a Module Set

**Warm-up (Day 107):**
1. No backend exists yet on first run — it must be bootstrapped manually outside
   Terraform.
2. It prevents concurrent applies from corrupting the same state via a race condition.
3. Because state can hold plaintext secrets — committing it to git would leak them.
4. VPC + subnets (public/private), IGW + route tables, a security group, and one EC2
   instance.

**Recall:**
1. It matches how teams actually own/change infrastructure independently (network rarely
   changes, database sizing changes more, compute changes every deploy), limiting blast
   radius — grouping by resource type wouldn't reflect that.
2. It re-points existing state entries to new addresses (e.g. into a module) without
   destroying and recreating real infrastructure.
3. It proves Terraform sees no actual infra change needed — only the state's internal
   representation moved.
4. One root state is simpler but has a larger blast radius; one state per module gives
   more isolation but adds `terraform_remote_state` plumbing.
5. In a variable marked `sensitive = true` (or a secrets manager) — never as a literal
   value in a `.tf` file.

---

## Day 109 — Security & Well-Architected: Least-Privilege Audit, Trusted Advisor, WAR Review

**Warm-up (Day 108):**
1. It matches independent ownership/change-cadence boundaries, limiting blast radius.
2. It re-pointed state entries to new module addresses without destroying/recreating
   resources.
3. It shows no actual infrastructure change was needed — proof the refactor was purely
   representational.
4. In a `sensitive = true` variable or a secrets manager; never as a literal in `.tf`.

**Recall:**
1. It flags resources (S3 buckets, IAM roles, KMS keys) shared *outside* the account/org
   — catching accidental exposure a manual policy read might miss.
2. Access Advisor shows unused permissions (last-accessed data) for pruning; Access
   Analyzer detects resources exposed outside the account/org boundary.
3. They're a free, independent second check on exactly the mistakes this phase's labs are
   prone to (open SGs, missing MFA).
4. Security and Cost Optimization — the other four pillars are touched implicitly by
   Phase 5's SRE work and the later FinOps deep-dive.
5. It keeps fixes version-controlled, reviewable, and reproducible, avoiding
   console/state drift.

---

## Day 110 — ★End-to-End: GitHub Actions → ECR → EKS/ECS, DNS + TLS, via Terraform

**Warm-up (Day 109):**
1. Resources shared outside the account/org — accidental public/cross-account exposure.
2. Security and Cost Optimization.
3. To keep fixes version-controlled and reproducible, avoiding drift.
4. modules/network, modules/compute, modules/database, modules/storage.

**Recall:**
1. So infrastructure changes ship through the same automated, reviewed pipeline as app
   changes, not a separate manual process.
2. For traceability — knowing exactly which commit produced a given running image, unlike
   a mutable `latest` tag.
3. It verifies the deploy actually works end-to-end; on failure the pipeline should stop
   and report failure rather than declaring a broken deploy successful.
4. Day 101 did it manually in console/CLI; Day 110 expresses it as
   `aws_acm_certificate`/`aws_route53_record`/`aws_acm_certificate_validation` resources
   applied declaratively.
5. ECS task-definition revision rollback, or `helm rollback`, depending on the compute
   path chosen.

---

## Day 111 — AWS SAA Drill Day + Resume Checkpoint #1

**Warm-up (Day 110):**
1. So infra changes ship through the same automated pipeline as app changes.
2. For traceability — knowing exactly which commit produced a running image.
3. To fail the pipeline loudly if the deploy didn't actually work.
4. ECS task-definition revision rollback, or `helm rollback`.

**Recall:**
1. Resilient Architectures → Day 95 (ASG/Multi-AZ); High-Performing Architectures →
   Day 101 (CloudFront/caching); Secure Applications → Day 93/96 (IAM/SG/NACL);
   Cost-Optimized Architectures → Day 93/99 (budgets/S3 lifecycle).
2. "Reduce NAT Gateway cost for AWS API calls from private subnets" maps to a VPC Gateway
   Endpoint (Day 97) — it removes NAT data-processing cost for S3/DynamoDB traffic.
3. Weak = vague verbs, no specifics; strong = action verb + what was built + specific
   technologies + outcome/scale.
4. Because the work's specifics are freshest right after a big demo-able milestone —
   waiting for a fixed calendar date risks losing precise detail.
5. Multi-AZ RDS failover — mentioned but not hands-on built this course.

---

## Day 112 — REVIEW (Observability, Terraform-on-AWS, WAR audit, end-to-end, SAA/resume)

**Cumulative quiz (Days 106–111):**
1. An average smooths out spikes; p99 surfaces tail latency pain an average hides.
2. CloudTrail answers who made an API call, when, and from where; Logs only capture
   application output.
3. On first run there's no backend to store state in yet — it must be bootstrapped
   manually, outside Terraform.
4. It re-points existing state entries to new addresses without destroying/recreating
   real infrastructure.
5. Access Analyzer detects resources exposed outside the account/org; Access Advisor
   shows unused permissions for pruning.
6. Security and Cost Optimization.
7. So infra changes ship through the same automated pipeline as app changes.
8. For traceability — knowing exactly which commit produced a running image.
9. A VPC Gateway Endpoint (reduces NAT cost for S3/DynamoDB traffic from private
   subnets).
10. Action verb + what was built + technology specifics + outcome/scale.

---

## Day 113 — Azure Mapping I: Subscriptions/RG, VNet vs VPC, Entra vs IAM, VM/Storage

**Warm-up (Days 106–111, mixed with earlier concepts — ⚠ partly outside file range):**
1. IaaS: AWS secures hardware/hypervisor/facilities, you secure OS-up; PaaS: AWS also
   manages the runtime, you manage app/data; SaaS: AWS manages nearly everything, you
   manage data/access/usage.
2. AZ — design failure-tolerance across Availability Zones within a region.
3. Users, groups, roles, policies.
4. It defines the total private IP address range available to the VPC, which subnets
   then carve into smaller ranges.

**Recall:**
1. `az group delete` deletes the entire Resource Group and everything inside it in one
   command — no direct AWS equivalent of that strength.
2. Entra ID is a full org-directory service by default (like on-prem AD); AWS IAM is
   deliberately not a directory service (Identity Center bolts that on separately).
3. An NSG can attach at the subnet or NIC level; AWS SG is instance/ENI-level only.
4. A Public IP resource attached to a resource plays the IGW's role — Azure has no
   separate Internet Gateway object.
5. Blob Storage nests one extra level: Storage Account → containers → blobs, versus S3's
   flatter bucket → objects model.

---

## Day 114 — Azure Mapping II: AKS Deploy of the Helm Chart, Azure DevOps Glance

**Warm-up (Day 113):**
1. Deletes everything inside the Resource Group.
2. Entra ID is a full directory service by default; IAM is not.
3. NSG attaches at subnet or NIC level; SG is instance/ENI-level only.
4. A Public IP resource — no separate gateway object exists.

**Recall:**
1. AKS's control plane is free; EKS charges an hourly control-plane fee.
2. `kubectl`, Helm, and the chart itself — deployed with zero changes across clouds.
3. Workload Identity (Entra Workload ID) — the same OIDC-federation pattern as IRSA.
4. Both use a triggers → stages/jobs → steps YAML shape — vocabulary differs, mental
   model is identical.
5. Because GitHub Actions is already the project's built, public CI tool (Day 33) —
   today is recognition-level familiarity only.

---

## Day 115 — GCP Mapping: Projects, IAM Diffs, GKE, BigQuery Mention, Decision Framework

**Warm-up (Day 114):**
1. AKS's control plane is free; EKS's is billed hourly.
2. `kubectl`, Helm, and the chart itself.
3. Workload Identity (Entra Workload ID).
4. Because GitHub Actions is already the project's built, public CI tool.

**Recall:**
1. GCP's Project is the primary billing+resource boundary and is more granular than an
   AWS account or Azure subscription — teams often run dozens of projects.
2. GCP IAM policies inherit automatically down Org→Folder→Project→Resource; AWS has no
   equivalent automatic inheritance (Organizations SCPs act as a ceiling, not additive).
3. GKE Autopilot manages nodes entirely, billed per-pod resource rather than per-node —
   no direct EKS/AKS standard equivalent yet.
4. The same Helm chart deployed unmodified across EKS, AKS, and GKE — Kubernetes
   workload portability held across all three.
5. "I'm AWS-deep with working fluency in Azure/GCP core concepts, and can ramp on
   whichever cloud a team uses because the underlying patterns are the same shape
   everywhere."

---

## Day 116 — Multi-Cloud Reality: Portability Myths, Egress Economics, Hybrid Patterns

**Warm-up (Days 113–115):**
1. The Helm chart and `kubectl`/Helm workflow proved portable; cluster-creation syntax,
   IAM/identity models, and registry auth did not.
2. GKE Autopilot manages nodes entirely, billed per-pod.
3. GCP IAM inherits automatically down the resource hierarchy; AWS has no equivalent.
4. "I'm AWS-deep with working fluency in Azure/GCP core concepts, and can ramp on
   whichever cloud a team uses."

**Recall:**
1. Kubernetes proved the workload (chart/`kubectl`) portable; it did not make the
   underlying infrastructure (Terraform config, IAM model, managed-service APIs)
   portable.
2. Clouds price data leaving their ecosystem entirely higher than moving it within their
   own regions/network, making cross-cloud egress pricier than same-cloud cross-region
   transfer.
3. "Data gravity" is the inertia large datasets create — moving them is slow/expensive,
   quietly locking in cloud choice for data-heavy workloads.
4. Maintaining fluency and operational cost across 3 clouds' IAM/networking/CLI quirks is
   a real ongoing cost, not a one-time learning cost.
5. Multi-region-same-cloud — dramatically simpler and cheaper than genuine multi-cloud
   DR, which is reserved for extreme scale/regulatory cases.

---

## Day 117 — FinOps I: Cost Model, Tagging Strategy, Allocation, Unit Economics, Showback/Chargeback

**Warm-up (Day 116):**
1. Kubernetes proved the workload/chart portable, not the infrastructure.
2. Clouds price cross-cloud egress higher than same-cloud cross-region transfer.
3. Data gravity — the inertia of moving large datasets, locking in cloud choice.
4. Maintaining fluency/operational cost across 3 clouds is an ongoing, not one-time,
   cost.

**Recall:**
1. Inform, Optimize, Operate — today's tagging/allocation work belongs to Inform (making
   cost visible and understandable).
2. Untagged spend can't be attributed to a team/project/cost-center, so it's effectively
   invisible for accountability purposes even though it's real money.
3. Showback shows allocated cost as information only (no money moves); chargeback bills
   it internally — start with showback to build awareness without political friction
   before allocation accuracy/trust is established.
4. It ties spend to outcomes (cost per customer/transaction); a rising bill next to a
   faster-rising user base is healthy, while the same spend against flat usage is a
   crisis — raw spend alone can't distinguish these.
5. Splitting a shared resource's cost proportionally by a measurable consumption signal
   (e.g. a shared NAT Gateway split by historical data-processed volume) — not perfect,
   but reasoned and documented.

---

## Day 118 — FinOps II: Rightsizing, RI/Savings Plans/Spot, Storage Tiering, Kubecost

**Warm-up (Day 117):**
1. Inform, Optimize, Operate — today (rightsizing/commitments/spot/tiering) is Optimize.
2. Because it can't be attributed to a team/project/cost-center.
3. Showback is informational only; chargeback actually bills the team — start with
   showback to build trust first.
4. Splitting a shared resource's cost proportionally by a measurable consumption signal.

**Recall:**
1. On-demand for unpredictable/spiky workloads, commitments (RI/Savings Plans) for
   known-steady baseline usage, spot for interruption-tolerant workloads — most real
   workloads mix all three.
2. Convertible RIs buy the flexibility to change instance family during the term, in
   exchange for a smaller discount than Standard RIs.
3. EBS volumes still billing on stopped/orphaned instances (and orphaned snapshots), and
   Elastic IPs billed while not attached to a running instance.
4. Cost Explorer stops at node-level billing (it's an EC2 instance) — it can't see inside
   a node to attribute cost to the namespace/deployment/pod consuming it.
5. Cost broken down per namespace/deployment/label, including idle/unallocated capacity
   from over-requested resources — visibility Cost Explorer/CloudWatch alone can't
   provide.

---

## Day 119 — REVIEW (Azure/GCP mapping, multi-cloud reality, FinOps I & II)

**Cumulative quiz (Days 113–118):**
1. The Helm chart and `kubectl`/Helm workflow proved portable; cluster-creation syntax,
   IAM models, and registry auth did not.
2. GKE Autopilot manages nodes entirely, billed per-pod rather than per-node.
3. Clouds price cross-cloud egress higher than same-cloud cross-region transfer.
4. Data gravity — large datasets are slow/expensive to move, locking in cloud choice for
   data-heavy workloads.
5. Inform, Optimize, Operate.
6. Untagged spend can't be attributed to a team/project/cost-center.
7. Showback is informational (no money moves); chargeback actually bills internally —
   start with showback to build trust before allocation accuracy is proven.
8. On-demand for unpredictable/spiky, commitments for known-steady baseline, spot for
   interruption-tolerant workloads.
9. EBS volumes still billing on stopped/orphaned instances/snapshots, and idle Elastic
   IPs not attached to a running instance.
10. Cost Explorer stops at the node/instance billing boundary; Kubecost (or OpenCost)
    fills that gap using cluster metrics + billing data.

---

## Day 120 — ★Phase 4 Capstone: FinOps Report + AWS SAA/Terraform Associate Readiness Check

**Warm-up (Days 117–119):**
1. Inform, Optimize, Operate.
2. On-demand for unpredictable/spiky, commitments for known-steady baseline, spot for
   interruption-tolerant workloads.
3. Cost per namespace/deployment/label and idle/unallocated capacity — Cost Explorer
   alone stops at the node boundary.
4. The Helm chart, and the `kubectl`/Helm workflow, deployed unmodified across all three
   clouds' managed Kubernetes offerings.

*(Day 120's file has no separate "Recall questions" section — only the warm-up, followed
by Definition of done and the Day 121 preview.)*
