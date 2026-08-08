# Answer Key — Phase 2 (Days 31–60)

Trainer prep companion. Answers are grounded in each day's theory section; ⚠ marks
answers drawn from general knowledge beyond the day file — verify against official docs
before teaching.

---

## Day 031 — Git I: init/add/commit/log/diff, the Object Model

**Warm-up:**
1. ⚠ `sys_report.py` (the Phase 1 capstone tool) collects a system health report and
   writes it out on a schedule; it's triggered by a systemd `.timer` unit paired with a
   `.service` unit, not cron.
2. ⚠ A bash script is best for quick shell-command orchestration but has weak data
   structures/error handling; a Python script gives real data structures (dicts/lists),
   proper exception handling, and libraries (requests, json) — better once logic goes
   beyond chaining commands.
3. `/opt/fresher_project`.
4. It integrated the separate Phase 1 pieces (bash script, Python script, systemd timer,
   nginx serving output) into one working, end-to-end tool rather than isolated parts.

**Recall:**
1. Working directory → staging area (index) → repository (commits).
2. A commit object points to a tree (root snapshot) plus its parent commit(s) and
   author/message metadata; a tree object contains entries (name + mode + hash) for
   blobs (files) and nested trees (subdirectories).
3. Because objects are content-addressed by hash: changing a byte in a blob changes the
   blob's hash → changes the tree referencing it → changes every commit whose tree chain
   includes it, cascading forward through history.
4. `HEAD` is a reference (`.git/HEAD`) pointing at the current branch ref (which points
   to a commit), or directly at a commit if detached.
5. `git diff` compares working directory vs staging area; `git diff --staged` compares
   staging area vs the last commit (HEAD).
6. It computes the SHA hash a file's content would get if committed, without creating a
   blob object or touching the index/history — a preview hash.

---

## Day 032 — Git II: Branching, Merging, Conflicts, Stash

**Warm-up:**
1. Blob = raw file content (no name); tree = directory listing of blobs/trees; commit =
   pointer to a tree plus parent(s) and metadata.
2. The tip of the current branch (or a specific commit if HEAD is detached).
3. `git diff` = working dir vs staged; `git diff --staged` = staged vs last commit.
4. `git cat-file -p <hash>`.

**Recall:**
1. A branch is a movable pointer (a file under `.git/refs/heads/<name>`) to a commit —
   not a copy of any files.
2. Fast-forward when the target branch has no new commits since the feature branched
   (pointer just moves); a merge commit is created when both branches diverged
   (three-way merge from a common ancestor).
3. `<<<<<<< HEAD` = your current branch's version, `=======` = divider, `>>>>>>> branch`
   = the incoming branch's version; after resolving and removing all markers: `git add
   <file>` then `git commit`.
4. `git stash pop` applies the change AND removes it from the stash stack; `git stash
   apply` applies it but leaves it on the stack.
5. When you want to keep a visible merge commit / feature-branch boundary in history even
   though a fast-forward is possible.
6. It restores the working tree/index to the state right before the merge attempt,
   cleanly bailing out of an in-progress conflict.

---

## Day 033 — Git III: Remotes, GitHub, PR Workflow ★ Project Goes Public

**Warm-up:**
1. Fast-forward: no divergence since branching. Three-way: both branches diverged, needs
   a merge commit from the common ancestor.
2. `<<<<<<< HEAD`, `=======`, `>>>>>>> branch-name`.
3. Applies the stashed change to the working tree and removes it from the stash stack.
4. `-d` deletes only if already merged (safe); `-D` force-deletes regardless.

**Recall:**
1. It registers a named remote URL (conventionally `origin`) that `push`/`fetch`/`pull`
   can reference by name instead of the full URL.
2. `git fetch` downloads remote refs/objects without merging; `git pull` fetches and then
   merges (or rebases with `--rebase`) into the current branch.
3. Because `.gitignore` only prevents NEW/untracked files from being tracked — it doesn't
   affect files already tracked; `git rm --cached <file>` untracks it (keeps it on disk).
4. Branch → commit → push the branch → open a PR (base: main, compare: branch) → review
   the diff → merge (regular/squash/rebase) → delete the branch.
5. It collapses all the branch's commits into a single new commit on the target branch,
   producing linear history instead of preserving every intermediate commit.
6. To start building a real, visible track record immediately — recruiters look at
   GitHub activity — rather than waiting for an arbitrary "done" state.

---

## Day 034 — Git IV: Rebase, Reset/Reflog Rescue, Tags, Branching Strategies

**Warm-up:**
1. `git fetch` downloads without merging; `git pull` fetches and merges/rebases.
2. Because `.gitignore` only stops NEW files from being tracked; an already-tracked file
   needs `git rm --cached` to be untracked.
3. It squashed the PR's commits into a single clean commit on the target branch.
4. Setting the repository's visibility to Public.

**Recall:**
1. Merge preserves both branches' real commits plus a two-parent merge commit; rebase
   replays your commits on top of the target branch as brand-new commits (new hashes),
   producing linear history with no merge commit.
2. Because rebase rewrites commit hashes; anyone who already pulled the old commits ends
   up with a diverging, incompatible history, causing painful conflicts when syncing.
3. `--hard` discards staged AND working-directory changes to match the target commit;
   `--soft` only moves the branch pointer (keeps changes staged); `--mixed` (default)
   unstages but keeps working-directory changes.
4. Because commits aren't deleted immediately by reset/rebase — they stay in the object
   database, unreferenced, until garbage collection runs; `reflog` records every place
   HEAD has pointed, letting you find and recover the pre-mistake commit hash.
5. An annotated tag is its own Git object with tagger, date, and message; a lightweight
   tag is just a pointer with no metadata — annotated is preferred for releases.
6. Trunk-based, because it favors short-lived branches merged frequently, matching a
   fast CI feedback loop; Gitflow's long-lived branches suit slower, versioned releases.

---

## Day 035 — REVIEW (Git week: init → rebase/reflog/tags)

**Cumulative quiz:**
1. Blob (file content), tree (directory listing of blobs/trees), commit (tree + parents
   + metadata) — a commit points to a tree, which points to blobs and/or nested trees.
2. HEAD points to the current branch ref (or a commit if detached); during a rebase it
   moves commit-by-commit as each replayed commit is created, ending at the new tip.
3. `git diff` = working dir vs staging area; `git diff --staged` = staging area vs last
   commit.
4. Fast-forward when the target has no new commits since divergence; three-way merge
   when both branches have new, divergent commits (needs a common-ancestor merge).
5. `<<<<<<< HEAD` (your side) / `=======` (divider) / `>>>>>>> branch` (their side);
   next: edit to the correct content, remove markers, `git add <file>`, `git commit`.
6. `git stash pop` applies and removes the entry from the stack; `apply` applies but
   keeps it on the stack.
7. `git fetch` only downloads refs without merging; the safer habit is fetch-then-review
   before merging, since `git pull` merges immediately and can surprise you.
8. Because rebase rewrites commit hashes; if others already pulled the originals, their
   history diverges from yours, breaking their clones/syncs.
9. `--soft` keeps changes staged, `--mixed` (default) unstages but keeps working-dir
   changes, `--hard` destroys both to match the target commit.
10. Annotated tag — a full Git object with tagger/date/message, preferred for releases;
    lightweight tags are just a pointer with no metadata.

---

## Day 036 — Python for Ops I: requests, REST APIs, JSON, Error/Retry Patterns

**Warm-up:**
1. `python3 -m venv <dir>` to create; `source <dir>/bin/activate` to activate.
2. A list is an ordered, index-accessed sequence; a dict is a key-value mapping accessed
   by key.
3. ⚠ `argparse` gives automatic `-h/--help`, type conversion/validation, required/
   optional flag handling, and clear error messages, versus manually parsing `sys.argv`.
4. A Git object storing raw file content, with no filename or metadata attached.

**Recall:**
1. Because a hung request with no timeout can block/freeze an automation script
   indefinitely if the server never responds.
2. It raises an `HTTPError` on a bad status code instead of continuing silently; manually
   checking `status_code` is easy to forget since the success path never prompts for it.
3. `resp.json()` parses the body as JSON into a Python object; `resp.text` returns the
   raw response body as a string.
4. Because it spaces retries out exponentially so repeated attempts don't hammer a
   struggling service immediately, giving transient failures time to clear.
5. Because a GET is (assumed) safe to repeat; a POST often creates/mutates state, so
   retrying one that may have already succeeded risks duplicate side effects.
6. `requests.exceptions.ConnectionError` and `requests.exceptions.Timeout` (also
   `HTTPError` after `raise_for_status()`).

---

## Day 037 — Python for Ops II: Building CLIs, Logging Done Right, Config Files

**Warm-up:**
1. Timeouts prevent a hung network call from freezing automation code indefinitely.
2. It logs the message AND the full exception traceback automatically when called
   inside an `except` block.
3. Because it spaces retries out exponentially, giving transient failures time to
   resolve instead of hammering the target immediately.
4. `internet_reachable` (true/false); it fails safely because the check is wrapped in
   try/except, so a failed request just sets the field false instead of crashing.

**Recall:**
1. No log levels, no timestamps, no easy simultaneous file+console output, and no easy
   way to filter/silence output without changing code.
2. DEBUG < INFO < WARNING < ERROR < CRITICAL.
3. It automatically captures and logs the full exception traceback, not just the error
   message string.
4. Defaults → config file → environment variable → CLI flag (each layer overrides the
   previous).
5. It lets one CLI script expose multiple independent subcommands (each with its own
   arguments), dispatched via the parsed `dest` value.
6. It ties the logger's name to the module (`__name__`), making it easy to identify the
   source of a log line and configure/filter logging per module rather than globally.

---

## Day 038 — Python for Ops III: pytest, Project Layout, Type Hints

**Warm-up:**
1. Default → config file → CLI flag (env var layer optional).
2. The full exception traceback, not just the error message string.
3. Because a hung network call without a timeout can freeze automation indefinitely.
4. It lets a single CLI script expose multiple independent subcommands, each with its
   own arguments.

**Recall:**
1. Test files named `test_*.py` or `*_test.py`, with test functions named `test_*`.
2. That a specific exception type is raised when the wrapped code runs; the test fails
   if it isn't raised.
3. So the test is fast, deterministic, and doesn't depend on real network availability
   or an external service's state/rate limits.
4. It separates importable package code from test code and avoids accidentally
   importing the working directory instead of the intended package.
5. No — type hints are not enforced at runtime by default; `mypy` is the tool that
   actually checks them statically.
6. Pure functions (no I/O, no arg parsing) are trivial to call with controlled inputs
   and assert on outputs, making them far easier to test than code entangled with CLI
   wiring.

---

## Day 039 — Data Formats: YAML Deep Dive, JSON/TOML, jq/yq Mastery

**Warm-up:**
1. Default → config file → CLI flag override.
2. So the test stays fast, deterministic, and independent of real network state.
3. It separates importable package code from test code, avoiding accidental imports of
   the working directory.
4. `list[str]`.

**Recall:**
1. Because YAML's implicit typing treats unquoted `yes/no/on/off` (case-insensitive) as
   booleans; quoting the value forces it to be parsed as a string.
2. `|` (literal) preserves newlines exactly as written; `>` (folded) joins lines with
   spaces, collapsing single newlines.
3. It lets you define a reusable block once (`&name`) and reference/merge it elsewhere
   (`*name`, `<<: *name`) instead of repeating the same config.
4. `-r` outputs raw, unquoted strings instead of JSON-quoted strings — needed for piping
   into shell loops/commands.
5. `-i` edits the file in place, writing changes back to disk, instead of printing the
   result to stdout.
6. For flat, explicitly-typed tool configs (e.g., `pyproject.toml`) where TOML's
   `[section]` structure is clearer and less error-prone than YAML for non-nested data.

---

## Day 040 — CI/CD Concepts + GitHub Actions I: First Workflow

**Warm-up:**
1. `test_*`/`*_test.py` file naming, `test_*` function naming.
2. Because YAML implicitly parses unquoted `yes/no/on/off` as booleans.
3. `-r` outputs raw (unquoted) strings instead of JSON-quoted output.
4. YAML (`config.yaml`, migrated from `config.ini`).

**Recall:**
1. Continuous Integration runs automated build/lint/test on every push to catch
   breakage immediately; CD extends CI to automatically package (Delivery) and/or ship
   (Deployment) the result to an environment.
2. Because the runner starts with an empty filesystem — the repo isn't present until
   `actions/checkout` fetches it.
3. `uses:` runs a reusable, packaged action from the marketplace; `run:` executes raw
   shell commands directly.
4. `push`, `pull_request`, `workflow_dispatch` (manual), `schedule` (cron) —
   `workflow_dispatch` lets you trigger a run manually.
5. So failures are clearly attributed to lint vs test, and each shows independent status
   in the PR checks instead of one conflated job.
6. A file produced by a job (e.g., a test report or built artifact) that can be
   uploaded/downloaded/passed between jobs; example: a `report.xml` junit report
   uploaded via `actions/upload-artifact@v4`.

---

## Day 041 — GitHub Actions II: Matrix, Secrets, Caching, Badges ★ Full CI Green

**Warm-up:**
1. `uses:` runs a marketplace action; `run:` executes raw shell commands.
2. Because the runner's filesystem starts empty — nothing can operate on the repo until
   it's checked out.
3. `workflow_dispatch`.
4. It let you download the artifact from the run's summary page after completion and
   inspect it.

**Recall:**
1. That code/tests work correctly across multiple versions/environments at once, rather
   than just one.
2. GitHub automatically masks/redacts any log line containing the exact secret value, so
   it never appears in plaintext in the logs.
3. `GITHUB_TOKEN` is an automatic, scoped, short-lived token GitHub injects into every
   run for talking back to the repo/API — unlike a secret you create, you never generate
   or store it yourself.
4. A hash of the `requirements.txt` file's content.
5. It enforces that the named checks (e.g., `test`, `lint`) must pass before a PR can be
   merged into `main`, usually alongside requiring PRs (no direct pushes).
6. Because the cache key is derived from the file's content hash — any content change
   produces a new hash, causing a cache miss and a fresh install (cached under the new
   key).

---

## Day 042 — REVIEW (Python-for-Ops, Data Formats, GitHub Actions week)

**Cumulative quiz:**
1. Because a hung request with no timeout can freeze an automation script indefinitely.
2. It raises an exception on a bad status code instead of continuing silently; manually
   checking `status_code` is easy to forget since the success path doesn't prompt for it.
3. So the test stays fast, deterministic, and independent of real network/service
   availability.
4. Separating importable package code (`src/`) from test code, avoiding accidental
   imports of the working directory instead of the intended package.
5. Because YAML's implicit typing parses unquoted `yes/no/on/off` (case-insensitive) as
   booleans.
6. `|` preserves newlines literally; `>` folds (joins) lines with spaces.
7. `jq -r` outputs raw/unquoted strings; `yq -i` edits the YAML file in place.
8. `uses:` runs a reusable marketplace action; `run:` executes raw shell commands.
9. That code/tests pass across multiple version/environment combinations in parallel,
   not just one.
10. That the specified status checks (e.g., test, lint) must pass before a PR can be
    merged into `main`.

---

## Day 043 — Jenkins I: Install (Container), UI, Freestyle vs Pipeline, First Jenkinsfile

**Warm-up:**
1. CI = automated build/lint/test on every push; CD extends CI to automatically package
   (Delivery) and/or ship (Deployment) to an environment.
2. `${{ secrets.X }}` protects a stored credential value; GitHub masks/redacts it
   automatically if a workflow accidentally logs it.
3. That the required status checks must pass before merging into `main`.
4. It runs the same job across multiple version/environment combinations in parallel,
   proving support across all of them at once.

**Recall:**
1. It keeps Jenkins disposable and reproducible without installing a JVM + Jenkins
   package on the host; the named volume persists Jenkins state across container
   restarts/recreations.
2. Pipeline job — its config (the Jenkinsfile) lives in the repo under version control,
   reviewable via PR; a Freestyle job is UI-configured only and subject to config drift.
3. A `Jenkinsfile` is the pipeline-as-code definition (declarative or scripted), usually
   at the repo root; a Pipeline job "from SCM" reads it directly from the Git repo it's
   pointed at.
4. Declarative — the recommended, more structured starting point (scripted/raw Groovy is
   more powerful but more complex).
5. The auto-generated initial admin password needed to complete the setup wizard.
6. ⚠ The base Jenkins image (`jenkins/jenkins:lts`) has no Python/pip installed, so a
   Python-based build step fails until an agent/container with the right tooling is used.

---

## Day 044 — Jenkins II: Agents, Credentials, Parameters; GitLab CI Compare

**Warm-up:**
1. Pipeline — it's version-controlled (Jenkinsfile in the repo), reviewable and free of
   config drift, unlike a UI-configured Freestyle job.
2. From the Git repo it's pointed at ("Pipeline script from SCM"), reading the
   `Jenkinsfile` there.
3. Missing Python/pip tooling inside the base Jenkins controller image.
4. Declarative.

**Recall:**
1. It runs that stage's steps inside a fresh, purpose-built container with exactly the
   needed tools, instead of installing tooling directly onto the thin, shared Jenkins
   controller image.
2. Mounting the Docker socket gives the container root-equivalent control over the
   host's Docker daemon — effectively host-root-level power, acceptable for a lab but a
   real security concern in production.
3. It automatically masks the value (shows `****`) in Console Output whenever it's
   referenced via `credentials('id')`.
4. It lets the user choose values (e.g., target environment, log level) via a form at
   trigger time ("Build with Parameters").
5. Structurally similar: both are YAML with jobs/stages and `script:`/`run:` steps; a
   Jenkinsfile differs by being Groovy-based (declarative or scripted), not YAML.
6. Because the underlying concepts (trigger, stage/job, artifact, secret) map across all
   three tools even though syntax differs — this transferable model is what interviewers
   probe for.

---

## Day 045 — Docker I: Images vs Containers, run/exec/logs/inspect, Lifecycle, Registries

**Warm-up:**
1. Missing tooling (e.g., Python) on the Jenkins controller — a Docker-based agent runs
   the stage inside a container with the tools it needs instead.
2. It masks the value automatically (shows `****`) in the console log.
3. It lets the user pick values (environment, log level, etc.) at trigger time via a UI
   form.
4. Both are YAML files with stages/jobs and shell-command steps, structurally close to
   each other.

**Recall:**
1. An image is a read-only, layered filesystem snapshot + metadata (a template); a
   container is a running (or stopped) instance of an image with its own writable layer
   and process namespace.
2. Because containers share the host kernel (Linux namespaces + cgroups) instead of
   virtualizing a full OS with a separate kernel boot, like a VM does.
3. `docker run` creates and starts a NEW container from an image; `docker exec` runs a
   command inside an ALREADY-running container.
4. Because a change made via `docker exec` lives only in that container's writable
   layer, which is destroyed by `docker rm`; a fresh `docker run` starts a new container
   from the unchanged image.
5. Because `:latest` is a moving tag that can point to a different image build over
   time, breaking reproducibility; a specific tag pins the exact version.
6. It automatically removes the container as soon as it exits — a good default for
   throwaway/test containers so they don't accumulate.

---

## Day 046 — Docker II: Dockerfile, Layers/Cache, Multi-Stage Builds — Containerize the Project

**Warm-up:**
1. An image is a read-only template; a container is a running/stopped instance of it
   with its own writable layer.
2. Because that change lives only in the container's writable layer, discarded when the
   container is removed.
3. Because `:latest` is a moving target that can silently point to a different build
   over time, breaking reproducibility.
4. That the project's tests ran successfully inside a clean `python:3.12-slim` container
   via a bind mount, without installing anything on the host.

**Recall:**
1. So code changes (frequent) don't invalidate the cached dependency-install layer —
   dependencies only reinstall when `requirements.txt` itself changes.
2. It shows every layer of an image and its size, useful for spotting which instruction
   is bloating the image.
3. It keeps build tools/toolchains out of the final image by building artifacts in one
   stage and copying only the needed output into a slim final stage.
4. With `CMD`, extra args to `docker run image <args>` replace the default command
   entirely; with `ENTRYPOINT`, extra args are appended as arguments to the fixed
   executable.
5. It trims the build context sent to the Docker daemon by excluding files; it does NOT
   affect what's inside the running container, only what's uploaded as build context.
6. Because `COPY`/`RUN` cache invalidates on content changes; copying dependency
   manifests before source code means only dependency changes (rarer) bust that cached
   layer, not every code edit.

---

## Day 047 — Docker III: Volumes, Bind Mounts, Networks, Compose v2

**Warm-up:**
1. Because dependency changes are rarer than code changes; ordering deps first keeps the
   install layer cached across code-only edits.
2. It keeps heavy build toolchains out of the final image by copying only the built
   artifact from a builder stage into a slim final stage.
3. `CMD`'s default is replaced entirely by extra `docker run` args; `ENTRYPOINT` stays
   fixed and extra args are appended to it.
4. Because that edit lived only in the container's writable layer, destroyed on
   `docker rm`; a fresh `docker run` starts from the unchanged image.

**Recall:**
1. Named volume when Docker should own/manage the storage (e.g., database data,
   persistent app state); bind mount when you need to control the exact host path and
   see/edit files from the host (e.g., dev source mounts, config injection).
2. Because the default bridge network provides no built-in DNS resolution by container
   name — only a user-defined network does.
3. Automatic DNS-based name resolution between containers on that network, letting them
   reach each other by container name instead of hardcoded IPs.
4. It automatically creates a shared network for all services in the compose file and
   makes service names resolvable as hostnames, with zero manual network commands.
5. `docker compose down` stops/removes containers and networks but keeps named volumes;
   `docker compose down -v` also removes the named volumes (data loss).
6. To prevent the running container from modifying source files on the host, while still
   letting host-side edits be reflected inside the container.

---

## Day 048 — Docker IV: Image Hygiene — Non-Root, Healthcheck, Scan ★ Hardened Image from CI

**Warm-up:**
1. Named volume: Docker-managed persistent data (e.g., a database). Bind mount:
   host-controlled path for live editing/config injection.
2. Because they're on the default bridge network, which has no name-based DNS
   resolution (only a user-defined/Compose network does).
3. A shared network with automatic DNS resolution of service names as hostnames.
4. `down` keeps named volumes; `down -v` also deletes them.

**Recall:**
1. Because if an attacker escapes the app into the container's OS layer, running as
   root there means they're one misconfiguration, kernel exploit, or mounted socket away
   from full host root — a real privilege-escalation risk, not just a lint nitpick.
2. It lets the orchestrator periodically probe the container's actual health and use
   `healthy`/`unhealthy`/`starting` status to decide whether to route traffic to it or
   restart it (e.g., Compose `depends_on: condition: service_healthy`, k8s probes).
3. It scans the image for HIGH/CRITICAL known CVEs and fails the CI job (nonzero exit)
   if any are found, turning the scan into a real merge/publish-blocking gate.
4. Because `GITHUB_TOKEN` is an automatic, scoped, short-lived token GitHub already
   injects into every workflow run, and can be granted `packages: write` permission to
   push to GHCR without creating a new secret.
5. Separate jobs give clear, independent pass/fail status per stage and let
   `deploy`/`publish` depend on `test`/`scan` passing first (`needs:`), preventing
   broken or vulnerable code from being published — one combined job loses that gating.
6. To confirm the pipeline actually did what it claims — pulling and running the real,
   CI-published artifact verifies reality, not just that the automation reported
   success.

---

## Day 049 — REVIEW (Jenkins + Docker week)

**Cumulative quiz:**
1. It runs that stage's steps inside a fresh container with the exact tools needed,
   instead of installing tooling on the thin Jenkins controller.
2. It grants the container root-equivalent control over the host's Docker daemon —
   acceptable for a lab, a real production security concern.
3. It shows `****` automatically in Console Output wherever the credential is
   referenced.
4. An image is a read-only template/filesystem snapshot; a container is a running (or
   stopped) instance of it with its own writable layer.
5. So code changes (frequent) don't force a dependency reinstall — only changes to the
   dependency manifest invalidate that cached layer.
6. It keeps build toolchains/compilers out of the final image by building artifacts in
   an intermediate stage and copying only the needed output into a slim final stage.
7. Named volume: Docker-managed persistent data (e.g., DB storage). Bind mount:
   host-controlled path for live dev editing/config injection.
8. Because the default bridge network provides no built-in DNS resolution by container
   name — only a user-defined (or Compose-created) network does.
9. Because root inside a container is one escape/exploit away from effective host-root
   access — a genuine privilege-escalation risk.
10. It scans the image for HIGH/CRITICAL CVEs and fails the CI job (nonzero exit) if any
    are found, making the scan a real merge/publish-blocking gate.

---

## Day 050 — Compose a 2-Service App, Env-Based Config, Intro 12-Factor

**Warm-up:**
1. Because dependency changes are rarer than code changes; ordering deps first keeps
   the install layer cached across code-only edits.
2. A shared network plus automatic DNS resolution of service names as hostnames.
3. Because an attacker escaping the app into the container OS is then root there — one
   step from host-level compromise.
4. `test` → `scan` → `publish` (build+scan+publish to GHCR).

**Recall:**
1. Config (store settings in the environment, not code), Backing services (treat a DB
   as an attached, swappable resource via connection string), Processes (keep them
   stateless, persist state to a backing service/volume) — any three of the seven
   covered principles are acceptable.
2. It prevents the app from starting and hammering a database that isn't actually ready
   yet — avoiding a real startup race condition, not just controlling ordering.
3. So connection details are swappable per environment without code changes, and are
   never hardcoded/committed in source.
4. That a runtime environment-variable override takes precedence over the value the
   service would otherwise use.
5. Because it can contain real local secrets and must never be committed to git
   regardless of project scale.
6. Because a CLI/reporting tool isn't a network-facing service exporting a listening
   port — factor VI doesn't cleanly apply to it.

---

## Day 051 — Ansible I: Inventory, Ad-Hoc Commands, Modules, Idempotency

**Warm-up:**
1. Because it prevents the app from starting and hitting a database that isn't ready
   yet, avoiding a real startup race condition.
2. So they're swappable per environment and never hardcoded/committed to source.
3. ⚠ Config — store settings in the environment, not in code (any one of the seven
   12-factor principles from Day 50 is acceptable).
4. Because it may contain real local secrets and must never be committed regardless of
   project scale.

**Recall:**
1. Because it talks over plain SSH and requires no persistent daemon/agent on managed
   nodes — a managed node just needs SSH access and Python.
2. `command` runs a raw command directly with no shell features (pipes/redirects);
   `shell` runs it through a shell, needed when pipe/redirect syntax is required.
3. That the task's target state was already correct — Ansible checked current state and
   made no change because it was already as desired.
4. Because a raw `useradd` command errors on a second run (the user already exists),
   whereas the `user` module checks current state first and only acts if needed.
5. It shows what tasks WOULD change without applying anything; it doesn't guarantee full
   accuracy for tasks that can't predict their own effect (e.g., raw `command`/`shell`).
6. `ok`, `changed`, `failed`, `unreachable`.

---

## Day 052 — Ansible II: Playbooks, Variables/Facts, Handlers, Jinja2 Templates

**Warm-up:**
1. Because it talks over SSH with no persistent daemon needed on managed nodes.
2. `command` executes directly with no shell features; `shell` runs through a shell and
   supports pipes/redirects.
3. That the task made no change — the target was already in the desired state.
4. It shows what changes WOULD be made, without applying anything.

**Recall:**
1. A play targets a group of hosts with a set of settings (e.g., `become`); a task is
   one individual action (module call) within a play's ordered task list.
2. In the playbook (`vars:`), in inventory (`host_vars/`, `group_vars/`), or passed at
   runtime with `-e`; `-e` (extra vars) has the highest precedence, overriding all other
   sources.
3. The `setup` module; it runs automatically at the start of every playbook unless
   `gather_facts: false` is set.
4. Being notified by a task that reports `changed: true` via that task's `notify:`.
5. So a handler notified multiple times within one play only executes once, avoiding
   redundant actions (e.g., duplicate restarts) within the same run.
6. `template` renders a file through Jinja2, substituting variables/logic before
   writing it; `copy` ships a file byte-for-byte unchanged.

---

## Day 053 — Ansible III: Roles, Directory Layout, Galaxy, ansible-vault

**Warm-up:**
1. A play targets hosts/settings; a task is a single action within a play.
2. Being notified by a task reporting `changed: true`.
3. `template` renders Jinja2 variable substitution into the output file; `copy` ships
   the file unchanged.
4. Playbook `vars:`, inventory `host_vars`/`group_vars`, or `-e` at the command line —
   `-e` wins (highest precedence).

**Recall:**
1. `defaults/main.yml` has the lowest precedence and is meant to be overridden by
   callers of the role; `vars/main.yml` has higher precedence and holds role-internal
   constants not meant for casual override.
2. The standard role directory structure (`tasks/`, `handlers/`, `templates/`, `vars/`,
   `defaults/`, `files/`, `meta/`, each with a `main.yml` where applicable).
3. It lets you encrypt a single value inline (to paste into an otherwise-plaintext vars
   file), rather than encrypting an entire file.
4. Because the encrypted file's ciphertext reveals nothing without the vault password —
   the password itself is the actual secret and must never be stored in the repo.
5. `--ask-vault-pass` (interactive prompt) or `--vault-password-file <file>` (a
   gitignored file).
6. It doesn't scale — a role packages tasks, handlers, templates, vars, and defaults
   into a reusable, shareable unit, versus one giant unmanageable playbook.

---

## Day 054 — Ansible IV: Error Handling, Tags, Check Mode — Full-VM Playbook

**Warm-up:**
1. `defaults/main.yml` is lowest precedence and meant to be overridden; `vars/main.yml`
   is higher precedence, role-internal.
2. Because ciphertext is unreadable without the password, but the vault password itself
   is the actual key to the secrets and must be kept out of the repo.
3. The standard role directory layout (`tasks/`, `handlers/`, `templates/`, `vars/`,
   `defaults/`, `files/`, `meta/`).
4. A single large playbook doesn't scale/reuse well — a role packages tasks/handlers/
   templates/vars into a reusable unit.

**Recall:**
1. `block:`/`rescue:`/`always:` — `block` groups tasks (try), `rescue` handles failure
   (except), `always` runs regardless (finally).
2. Because raw `command`/`shell` tasks report `changed: true` unconditionally by default
   (they're not real modules that check state) — `changed_when` lets you redefine what
   actually counts as a change.
3. `always` runs regardless of any `--tags`/`--skip-tags` filter; `never` only runs when
   explicitly requested — a custom tag only runs when matched or excluded via those
   flags.
4. When partial failure across hosts is worse than stopping everything — e.g., a
   coordinated rollout where you don't want some hosts updated and others not.
5. Because it shows exactly what would change (with a file-level diff) before applying
   real changes, letting you catch mistakes before they hit a target you're unsure of.
6. Base packages → user creation → nginx role → project deployment — in that order
   because each later step depends on the prior ones already being in place.

---

## Day 055 — Dev-Tooling Glue: Makefiles, pre-commit, EditorConfig, SemVer + CHANGELOG

**Warm-up:**
1. `block:`/`rescue:`/`always:`.
2. Because raw `command`/`shell` tasks report `changed: true` unconditionally by
   default, not based on actual state comparison.
3. Base packages → user creation → nginx role → project deployment (in that dependency
   order).
4. It shows exactly what tasks would change (and the content-level diff) without
   actually applying anything.

**Recall:**
1. Because Make's parser specifically expects a tab character to introduce recipe
   lines — spaces are silently treated as invalid/broken syntax.
2. It tells `make` that a target name isn't a real file, so it should always run the
   recipe rather than skipping it because a same-named file exists.
3. It provides a way to declare and distribute hooks via a repo-tracked config file
   (`.pre-commit-config.yaml`) so every contributor gets the same hooks installed with
   one command, unlike raw `.git/hooks/` scripts, which aren't version-controlled/shared.
4. Indentation style/size, line endings, charset, and trailing-newline rules per file
   type — it helps because most editors respect it automatically regardless of which
   editor a contributor uses.
5. MINOR is a backward-compatible new feature; PATCH is a backward-compatible bug fix.
6. `Added`, `Changed`, `Fixed`, `Removed`.

---

## Day 056 — REVIEW (Ansible + Dev-Tooling week)

**Cumulative quiz:**
1. Because it communicates over SSH with no persistent agent/daemon required; a managed
   node just needs SSH access and Python.
2. `command` runs directly with no shell features; `shell` runs through a shell and
   supports pipes/redirects.
3. Being notified by a task reporting `changed: true`; it runs at play-end so multiple
   notifications within one play only trigger one execution.
4. `defaults/main.yml` — lowest precedence, meant to be overridden; `vars/main.yml` —
   higher precedence, role-internal.
5. Because the ciphertext is unreadable without the vault password, which is the actual
   secret and must never be stored in the repo.
6. `block:`/`rescue:`/`always:` (try/except/finally equivalent).
7. Because they report `changed: true` unconditionally by default, regardless of
   whether they actually changed anything.
8. It provides a shareable, version-controlled hook configuration
   (`.pre-commit-config.yaml`) that every contributor installs identically, unlike raw
   `.git/hooks/` scripts, which live outside tracked content.
9. Because Make's parser requires a tab to recognize a recipe line; spaces break it.
10. MINOR adds backward-compatible functionality; PATCH is a backward-compatible bug fix
    only.

---

## Day 057 — End-to-End Thread: Commit → Actions → Build+Scan → Ansible Deploys ★

**Warm-up:**
1. It shows exactly what would change (including file-content diffs) before actually
   applying the playbook.
2. Base packages → user creation → nginx role → project deployment.
3. That the named target isn't a real file, so `make` should always run its recipe.
4. Because the ciphertext is unreadable without the password, which is the true secret
   and must stay out of the repo.

**Recall:**
1. To follow least-privilege and limit blast radius — a compromised automation key can't
   be used to impersonate personal access, and can be scoped/rotated independently.
2. That the deploy job only runs after `test`, `scan`, and `publish` have all succeeded,
   gating deployment on passing quality/security checks.
3. So Ansible deploys the exact image CI just built and scanned, instead of re-deriving
   or guessing a tag that could drift or mismatch.
4. To ensure deployments only happen from the trunk branch (never from feature
   branches/PRs), keeping production deploys tied to reviewed, merged code.
5. (1) the GitHub-hosted runner reaching the VM over a network path (e.g., VPN/
   Tailscale) with SSH credentials, or (2) a self-hosted runner registered on the target
   VM so Ansible runs locally against `localhost`; ⚠ which option was actually used
   depends on the trainee's environment — verify against their notes.
6. Because a green pipeline only proves the automation reported success — verifying the
   artifact directly (image ID/digest/version string) confirms real-world state actually
   matches intent.

---

## Day 058 — Secrets Management: env vs files, GitHub Secrets, sops/age, History Scrubbing

**Warm-up:**
1. To limit blast radius/follow least privilege — a compromised automation key doesn't
   expose personal access and can be rotated/scoped independently.
2. That deploy only runs after test, scan, and publish have all succeeded.
3. To ensure deploys only happen from the trunk branch via reviewed, merged code, not
   from feature branches/PRs.
4. Verifying the deployed artifact directly confirms reality, since a green pipeline
   only proves the automation reported success, not that the target actually reflects
   it.

**Recall:**
1. Because image layers are inspectable (e.g., via `docker history` or extracting the
   layer filesystem) even if the running container never prints the value — the secret
   is baked in and retrievable by anyone who can pull/inspect the image.
2. `sops` encrypts specific values within a YAML/JSON file while leaving structure/keys
   readable (diff-friendly), addressing secrets needed by non-Ansible files (e.g., plain
   YAML/Kubernetes manifests) that `ansible-vault` doesn't directly serve.
3. Rotate/revoke the secret immediately — assume it's compromised the moment it's
   pushed, since anyone could have already cloned/forked/cached it.
4. `git filter-repo`/BFG rewrite history to remove the secret string going forward, but
   do NOT undo exposure to anyone who already has a copy; rotating the secret actually
   neutralizes the risk regardless of who has seen it.
5. Because pre-commit only protects contributors who install and run the hook locally; a
   CI-layer scan catches anything that slips past someone who skipped or bypassed
   pre-commit — defense-in-depth.
6. It needs to point to the file containing the `age` private key used to decrypt the
   `sops`-encrypted file.

---

## Day 059 — Phase 2 Mock Interview: Git, CI/CD Design, Docker Debugging

**Warm-up:**
1. That deploy only runs after test, scan, and publish have all succeeded.
2. Rotate/revoke the secret immediately, before doing any history rewrite.
3. Because pre-commit only catches issues for contributors who run it locally; CI
   scanning catches anything that slips through — defense-in-depth.
4. `sops` encrypts specific values within arbitrary YAML/JSON files (e.g., non-Ansible
   configs/manifests) while keeping structure diff-readable — something plain
   `ansible-vault` doesn't address outside Ansible's own var files.

**Recall:**
1. A blob (file content), a tree (updated directory listing referencing the blob), and a
   commit (pointing to that tree plus its parent) — chained blob→tree→commit.
2. `git bisect` automates a binary search across commit history to find which commit
   introduced a bug (`start`, mark `bad`/`good <hash>`); `git bisect run <script>`
   further automates it by letting Git run a script at each step to auto-decide
   good/bad instead of manual testing each step.
3. lint → test → build → scan → publish → deploy; `scan` sits between build and publish
   so a vulnerable image is caught and can block the release before it's ever pushed to
   a registry.
4. Most likely they're on the default bridge network instead of a user-defined/Compose
   network, which doesn't provide container-name DNS resolution.
5. Wrong/bloated base image, a missing multi-stage build (build tools left in the final
   image), and no `.dockerignore` (or cache-busting `COPY . .` before deps).
6. Because file ownership/permissions can differ between the local host user and the
   non-root `USER` inside the container in CI — a mounted directory/file not owned by
   that UID causes permission errors that may not surface locally under different
   ownership conditions.

---

## Day 060 — ★ Phase 2 Capstone: Public, Tested, Containerized, CI-Built, Deployed

**Warm-up:**
1. lint → test → build → scan → publish → deploy; `scan` sits between build and publish
   so a vulnerable image can be blocked before it's ever published.
2. Being on the default bridge network instead of a user-defined/Compose network, which
   lacks name-based DNS resolution.
3. ⚠ Depends on the trainee's actual Day 59 gap list — pull the specific weakest area
   from their real notes rather than assuming one here.
4. A blob, a tree, and a commit — chained blob→tree→commit, with the commit's tree
   matching the new blob and pointing to its parent commit.

**Recall:**
1. Public repo with real incremental history; automated tests running in CI; a
   hardened, scanned, published container image; a full CI pipeline (lint/test/scan/
   publish, branch-protected); an idempotent Ansible deployment wired into CI; correctly
   handled secrets.
2. Because it removes all tribal knowledge/assumptions — it proves the documented steps
   genuinely work for someone (or something) with zero prior context, the real bar for a
   portfolio project.
3. The functional README documents what the project is and how to run it for a user;
   `docs/pipeline-overview.md` documents the CI/CD pipeline architecture and evidence for
   a reviewer/recruiter/interviewer audience.
4. Because verifiable evidence (links to real runs, package pages) is checkable by a
   skeptical reader, whereas prose claims alone can't be independently confirmed.
5. MAJOR (`v1.0.0`), because it marks completion of a full, stable, demonstrable system
   (Phase 2's capstone) — a meaningful milestone rather than an incremental feature/fix.
6. Because publishing is an outward-facing, hard-to-fully-reverse action — a review
   catches inaccuracies or tone issues before they're seen publicly, rather than
   treating publishing as just another automated checklist step.

Related: [[README|devops-training]]
