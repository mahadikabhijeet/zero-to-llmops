# Answer Key — Phase 1 (Days 1–30)

Trainer prep companion. Answers are grounded in each day's theory section; ⚠ marks
answers drawn from general knowledge beyond the day file — verify against official docs
before teaching.

---

## Day 001 — Linux Architecture, Boot Process, systemd Basics

**Warm-up:** Day 1 has no prior-material warm-up; it opens with 3 baseline questions
instead (no fixed answers — trainer judges responses):
1. Open-ended: gauge the trainee's prior terminal/OS exposure.
2. Open-ended: gauge their working definition of "open source" (code is publicly
   viewable/modifiable/redistributable under a license).
3. Open-ended: gauge whether they already distinguish kernel (core program managing
   hardware/processes/memory/files) from OS (kernel + surrounding system).

**Recall:**
1. Order: firmware (BIOS/UEFI) → GRUB → initramfs (loaded by GRUB alongside the kernel)
   → kernel init → PID 1 exec (systemd). I.e. firmware, GRUB, kernel init, initramfs,
   PID 1 exec becomes: firmware → GRUB → kernel decompresses/inits using initramfs →
   PID 1 exec.
2. PID 1 on modern RHEL/Fedora is **systemd**, the first userspace process and parent
   of all others. It replaced the older **SysV init**.
3. "Loaded" shows the unit file's path and whether it's enabled; "Active" shows the
   current run state (e.g. active/inactive/failed) plus how long it's been in that
   state.
4. `journalctl -b` shows logs from the **current** boot; `journalctl -b -1` shows the
   **previous** boot's logs (requires persistent journal storage to be enabled).
5. initramfs exists because the kernel needs drivers (e.g. for LVM/RAID/storage
   controllers) to find and mount the real root filesystem, but those drivers aren't
   available until something is mounted — initramfs is a small RAM-based root that
   carries just enough to bridge that gap.
6. Two examples: `systemd-analyze` (total boot time breakdown), `systemd-analyze
   blame` (lists units ranked by how long each took to start). (`critical-chain` is a
   third valid answer — dependency chain that delayed boot.)

---

## Day 002 — Core CLI Tools, File Management, I/O Redirection

**Warm-up:**
1. Firmware (BIOS/UEFI) → GRUB → initramfs/kernel init → PID 1 (systemd) → login
   prompt.
2. PID 1 is systemd, the first userspace process; it starts and supervises every other
   process on the system.
3. `systemctl status` shows full detail (Loaded/Active/Main PID/recent logs);
   `systemctl is-active` just prints the terse state (e.g. "active") and sets an exit
   code — good for scripting.
4. Vendor unit files live in `/usr/lib/systemd/system/`; admin-created/override files
   live in `/etc/systemd/system/`.
5. `journalctl -b` shows the journal for the current boot only.

**Recall:**
1. FD 0 = stdin, FD 1 = stdout, FD 2 = stderr. `2>&1` duplicates FD 2 to point at
   wherever FD 1 currently points (merges stderr into stdout).
2. `> f 2>&1` first redirects stdout to file `f`, then points stderr at (the now
   redirected) stdout — both land in `f`. `2>&1 > f` first points stderr at wherever
   stdout currently is (the terminal), then redirects stdout to `f` — stderr still
   goes to the terminal because the duplication happened before the stdout redirect.
3. Deleting the original: a symlink breaks (dangling, target gone) because it just
   holds a path; a hard link survives fully because it points to the same inode, and
   the file's data isn't removed until link count reaches zero.
4. Use `find` when you need accuracy for files that may have changed recently or when
   no index exists; use `locate` for speed against a prebuilt database. `locate`'s
   caveat: its database (`updatedb`) is only refreshed periodically, so very recent
   files may not show up.
5. `tee` writes its input to a file **and** passes it through to stdout simultaneously
   (or to further pipeline stages); `>` only redirects to a file, ending the stream
   there.
6. With `;`, the second command always runs regardless of the first's exit status.
   With `&&`, the second command does NOT run if the first command fails (non-zero
   exit).

---

## Day 003 — Users & Groups, /etc/passwd|shadow|group, su vs sudo

**Warm-up:**
1. FD 0/1/2 = stdin/stdout/stderr; `2>&1` merges stderr into wherever stdout points.
2. `/opt` holds third-party/add-on software per the FHS; the project lives there
   because it's a self-contained, non-distro-managed application.
3. Hard link shares the same inode as the original (survives deletion of the
   original); a symlink is a path reference that breaks if the original is deleted.
4. `journalctl -b` shows logs for the current boot only.
5. `tail -f` follows a file live as new lines are appended — used on Day 2 to watch
   `/var/log/messages` (or the journal) update in real time while another terminal
   generated a log entry.

**Recall:**
1. The `x` in the password field of `/etc/passwd` means "the real hashed password is
   stored in `/etc/shadow`, not here."
2. `-a` (append) must be used with `usermod -G`, otherwise `-G` **replaces** the user's
   entire supplementary group list — omitting `-a` silently removes them from every
   other group they were in (e.g. losing `wheel`/sudo access).
3. `su -` requires the **target user's** password and gives a full login shell with
   their environment; `sudo` requires **your own** password and runs a single command
   as another user (default root) per `/etc/sudoers` policy.
4. `/sbin/nologin` prevents interactive login for a service account — it should never
   be logged into directly, only used to own/run processes, reducing attack surface if
   its credentials leak.
5. `visudo` safely edits `/etc/sudoers` because it syntax-checks the file before
   saving, preventing a broken sudoers file from locking out all sudo access; editing
   directly with `vim` risks exactly that.
6. UID 1–999 is typically reserved for system/service accounts on RHEL/Fedora (exact
   cutoff defined in `/etc/login.defs`); 1000+ is for regular human users.

---

## Day 004 — Permissions Deep: rwx/Octal, umask, SUID/SGID/Sticky, ACLs

**Warm-up:**
1. Fields (in order): username:password(x):UID:GID:GECOS(comment):home directory:shell.
2. Because it should never be an interactive login account — reduces risk if its
   credentials are ever exposed.
3. Using `usermod -G` without `-a` wipes all of the user's existing supplementary
   group memberships, replacing them with only the groups listed.
4. `su -` asks for the target user's password; `sudo` asks for your own password.
5. `wheel` grants sudo access on RHEL/Fedora by convention (`%wheel ALL=(ALL) ALL` in
   sudoers).

**Recall:**
1. `rwxr-x---` = owner rwx(7), group r-x(5), other ---(0) = **750**. `640` symbolic =
   owner rw- , group r--, other --- = `rw-r-----`.
2. umask 022 subtracts from the max defaults: files start at 666 → 644 (rw-r--r--);
   directories start at 777 → 755 (rwxr-xr-x). The difference exists because files
   never get the execute bit by default (umask only removes bits, and the base for
   files excludes x), while directories need `x` to be traversable.
3. SGID on a directory makes new files/subdirectories created inside inherit the
   **directory's group** instead of the creating user's primary group — useful for
   `/opt/fresher_project/logs` so files written by `svc_fresher` still end up owned by
   the shared `devops-trainees` group.
4. The classic SUID example is `/usr/bin/passwd`: it must run with root's privileges so
   an ordinary user can write their new hashed password into the root-owned
   `/etc/shadow` file.
5. ACLs let you grant specific permissions to additional individual users or groups
   beyond the single owner and single group that plain rwx supports.
6. `chattr +i` makes a file immutable — even root cannot modify or delete it (without
   first unsetting the attribute), which plain permissions (which root can always
   override) don't protect against.

---

## Day 005 — Text Processing I: grep and Regex Fundamentals

**Warm-up:**
1. `rwxr-x---` = 750.
2. SGID on a directory makes new files inherit the directory's group instead of the
   creator's primary group.
3. So it would never be usable as an interactive login shell, limiting risk.
4. ACLs let you grant permissions to specific additional users/groups beyond the
   single owner/group rwx model supports.
5. The sticky bit's classic example is `/tmp`: only a file's owner (or root) can
   delete/rename it inside a world-writable sticky directory.

**Recall:**
1. `^` anchors to the start of a line, `$` anchors to the end of a line; `^$` alone
   matches a completely blank line.
2. In default (BRE) grep, `+` is a literal character, not a quantifier — `grep
   'a+b'` looks for literal "a+b". With `-E` (ERE), `+` becomes "one or more of the
   preceding" — `grep -E 'a+b'` matches one-or-more a's followed by b.
3. `-l` prints only the **filenames** that contain at least one match; `-c` prints the
   **count of matching lines** per file.
4. `grep -Ev '^\s*(#|$)'` (or the two-step `grep -v "^#" file | grep -v "^$"`).
5. Exit code 0 = at least one match found, 1 = no match found, 2 = an error occurred
   (e.g. bad pattern, unreadable file).
6. ⚠ `[[:digit:]]` is locale-aware and guaranteed to match only digit characters
   according to the current locale's character classification, whereas `[0-9]` is
   technically a byte-range that can behave unexpectedly under certain locales/
   collation settings (e.g. matching unintended characters) — flagged as general
   regex/locale knowledge, verify: `man 7 locale` and grep's POSIX bracket expression
   docs.

---

## Day 006 — Text Processing II: sed, awk, sort/uniq/cut/tr/paste

**Warm-up:**
1. `^$` matches a blank line (a line with nothing between start and end anchors).
2. ERE needs `-E`; with `-E`, `+`, `?`, and `{}` become active quantifiers without
   backslash-escaping (in BRE they're literal characters, or need `\+`, `\?`, `\{n,m\}`
   to work as quantifiers).
3. `grep -Ev '^\s*(#|$)'`.
4. SGID made new log files created under `/opt/fresher_project/logs` inherit the
   directory's group (`devops-trainees`) rather than the creating user's primary group.
5. 0 = match found, 1 = no match, 2 = error.

**Recall:**
1. `uniq -c` only counts consecutive duplicate lines — it doesn't scan the whole file
   for a value, so input must be sorted first to bring all identical lines together.
2. `sed 's/x/y/'` replaces only the **first** occurrence of x per line; `sed
   's/x/y/g'` replaces **all** occurrences per line.
3. `$0` is the whole current line/record; `NF` is the number of fields in the current
   record; `NR` is the current record (line) number, running total across the input.
4. `command | sort | uniq -c | sort -rn` — sorts, counts consecutive duplicates, then
   sorts numerically descending so the most frequent value appears first.
5. `sed -i.bak` performs the in-place edit but first saves an unmodified copy with a
   `.bak` suffix, giving you an undo path; plain `sed -i` overwrites with no backup.
6. `awk -F: '$3 >= 1000 {print $1}' /etc/passwd` (add `&& $3 < 65534` to also exclude
   `nobody`, per the day's Lab Part 2 example).

---

## Day 007 — REVIEW (Days 1–6)

**Cumulative quiz:**
1. Firmware (BIOS/UEFI) → GRUB → initramfs → kernel init → PID 1 exec.
2. `journalctl -b -1` shows the **previous** boot's log; it requires persistent
   journal storage (`Storage=persistent` in journald.conf) to be enabled — otherwise
   older boots' logs don't survive a reboot.
3. `>` truncates and redirects stdout to a file; `>>` appends stdout to a file; `2>&1`
   duplicates FD 2 onto wherever FD 1 currently points. Order matters because
   redirections are applied left to right: `> file 2>&1` sends stdout to `file`, then
   points stderr at that same file; `2>&1 > file` points stderr at the terminal (where
   stdout was *before* the redirect), then sends only stdout to `file`, leaving stderr
   on-screen.
4. `/etc/shadow` stores the hashed password and password-aging fields
   (lastchange/min/max/warn/inactive/expire), none of which are in `/etc/passwd`
   (which just has an `x` placeholder). It's root-only readable so unprivileged users
   can't obtain password hashes to attack offline.
5. Without `-a`, `usermod -G` replaces the entire supplementary group list, silently
   removing the user from every group not explicitly listed (e.g. dropping `wheel`).
6. `rwxr-x---` = 750. SGID on a directory makes new files/subdirs created inside
   inherit the directory's group rather than the creator's primary group.
7. `setfacl` grants extra permissions to specific additional users/groups beyond the
   single owner and single group that standard rwx supports.
8. `-E` (ERE) is needed for `+`, `?`, `|`, and `()` to work as metacharacters without
   backslash-escaping; in default BRE they're literal characters (or need escaping).
9. `uniq -c` only merges/counts **consecutive** identical lines, so unsorted input
   would undercount duplicates scattered non-adjacently; the idiom is
   `sort | uniq -c | sort -rn`.
10. `NF` = number of fields in the current record, `NR` = current record/line number,
    `$0` = the whole current line. Custom separator: `awk -F: '...'` (e.g. for
    `/etc/passwd`).

---

## Day 008 — Processes & Signals: ps/top, kill, nice, jobs, /proc

**Warm-up:**
1. `sort | uniq -c | sort -rn`.
2. `NF` = number of fields in the current awk record.
3. Because without `-a`, `-G` replaces (rather than adds to) the user's supplementary
   group list.
4. New files/subdirs created inside inherit the directory's group.
5. Firmware (BIOS/UEFI) → GRUB → initramfs/kernel init → PID 1 (systemd).

**Recall:**
1. SIGTERM is signal 15 — it can be caught/trapped by the process for graceful
   cleanup. SIGKILL (9) cannot be caught, blocked, or ignored — it terminates the
   process immediately and unconditionally.
2. STAT `Z` (zombie) means the process has finished executing but its exit status
   hasn't yet been collected (`wait()`-ed) by its parent — it's a dead process still
   holding a process-table entry.
3. `nohup` explicitly makes the process ignore SIGHUP (the hangup signal sent when the
   terminal closes), so it survives logout; a plain backgrounded `command &` is still
   attached to the terminal session and can receive SIGHUP when that session ends,
   killing it.
4. `kill -0 <pid>` sends no actual signal — it just checks (via the exit code) whether
   a process with that PID exists and you have permission to signal it, a safe
   liveness check.
5. `/proc/<pid>/fd/` contains symlinks representing every open file descriptor for
   that process, tying directly to Day 2's stdin/stdout/stderr (FD 0/1/2) concept —
   every open file, socket, or pipe shows up here as a numbered symlink.
6. Reach for `lsof -i :<port>` when you need to know which process owns a specific
   network port (e.g. debugging "what's already listening on 8080") — `ps` alone
   doesn't show port bindings.

---

## Day 009 — Package Management: dnf/rpm, Repos, History, rpm -q Forensics

**Warm-up:**
1. SIGTERM can be trapped/caught; SIGKILL cannot.
2. `kill -0 <pid>` checks whether a PID is alive (via exit code) without sending any
   real signal.
3. `ps aux` is BSD-style output oriented toward user-friendly columns; `ps -ef` is
   System V style and shows PPID clearly.
4. `/proc/<pid>/fd/` shows that process's open file descriptors as symlinks.
5. Open-ended — trainer confirms trainee actually ran the Day 7 NotebookLM catch-up
   and can name at least one topic re-quizzed.

**Recall:**
1. `rpm -qf <path>` tells you which installed package owns a given file — most useful
   for forensics: "where did this file come from / is it managed by a package?"
2. Config files at `/etc/passwd` are typically created dynamically by install scripts
   (e.g. `useradd`, post-install scriptlets) at package-install time rather than
   shipped as literal files inside the RPM payload, so no package "owns" them in the
   RPM database's file list.
3. `dnf history undo <id>` can cleanly roll back an entire transaction (including
   dependency changes) as a unit; raw `rpm -e` only removes what you explicitly name
   and has no built-in transaction-level undo.
4. Repo definitions live in `/etc/yum.repos.d/*.repo`; `gpgcheck=1` protects against
   installing tampered/unsigned packages by requiring a valid GPG signature match.
5. `rpm` is the low-level package/database tool (query, verify, install without
   dependency resolution); `dnf` is the higher-level daily-driver tool that resolves
   dependencies and talks to repos.
6. `dnf group install` installs a predefined bundle of related packages (e.g.
   "Development Tools") in one command, rather than requiring you to name and install
   each package individually.

---

## Day 010 — Storage I: Partitions, Filesystems, mount, /etc/fstab

**Warm-up:**
1. `rpm -qf <path>` tells you which package owns a given file on disk.
2. `dnf history undo <id>` rolls back a previous dnf transaction.
3. Repo files live in `/etc/yum.repos.d/*.repo`; `gpgcheck=1` enables GPG signature
   verification.
4. SIGTERM is trappable; SIGKILL is not.
5. `lsof -i :<port>` shows what's listening on a given port.

**Recall:**
1. UUIDs are stable identifiers for a filesystem, whereas `/dev/sdX` device names can
   shift across reboots (e.g. disk enumeration order changes) — using UUID in
   `/etc/fstab` avoids mounting the wrong device or failing to mount at all.
2. `mount -a` verifies that every entry in `/etc/fstab` is syntactically correct and
   mountable right now; running it before a reboot catches errors that would otherwise
   only surface as a boot failure.
3. xfs can only be **grown**, never shrunk, once created; ext4 supports both growing
   and shrinking (shrinking requires it to be unmounted).
4. `nofail` prevents the boot process from being blocked/halted if that particular
   device is missing at boot time (important for removable or network storage).
5. `lsof +D <dir>` (or `fuser -m <dir>`) shows which processes have open files or a
   working directory under the busy mountpoint.
6. GPT is required for UEFI boot (vs MBR for legacy BIOS); this traces back to Day 1's
   firmware discussion — the choice of BIOS vs UEFI at boot determines which partition
   table type the disk must use.

---

## Day 011 — Storage II: LVM, Swap, NFS Client + autofs Intro

**Warm-up:**
1. UUID doesn't shift across reboots the way `/dev/sdX` device names can.
2. `mount -a` protects you from discovering an fstab typo/error only at the next
   reboot (a boot failure).
3. `rpm -qf` tells you which package owns a given file.
4. xfs's key limitation is that it cannot be shrunk, only grown.
5. `lsof +D <dir>` finds what's holding a mountpoint busy.

**Recall:**
1. The three LVM layers are Physical Volume (PV, a raw disk/partition initialized for
   LVM), Volume Group (VG, a pool combining one or more PVs), and Logical Volume (LV,
   a resizable virtual partition carved from the VG). This indirection is what lets
   you resize/extend storage without repartitioning the underlying disks.
2. Extend the LV first (`lvextend`), then grow the filesystem on top of it
   (`xfs_growfs`/`resize2fs`) — never the reverse, since the filesystem can't exceed
   the space actually allocated to the LV.
3. `xfs_growfs` operates on a mounted xfs filesystem (online growth only, no shrink
   support ever); `resize2fs` works on ext4 and can grow either online or offline (and
   can also shrink, but only while unmounted).
4. `free -h` shows total/used/free swap alongside RAM. Swap is disk space used as
   overflow when RAM is exhausted — a performance safety net, not a RAM replacement.
5. autofs mounts on-demand and automatically unmounts after an idle period, reducing
   stale-mount risk and avoiding an always-open connection for storage that's only
   used occasionally, unlike a permanent fstab entry that's mounted continuously.
6. An fstab swap entry uses `swap` as the fstype and `none` as the mountpoint field
   (e.g. `UUID=... none swap sw 0 0`).

---

## Day 012 — systemd Deep: Unit Anatomy, Targets, Timers vs Cron ★

**Warm-up:**
1. PV (Physical Volume), VG (Volume Group), LV (Logical Volume).
2. Extend the LV first, then grow the filesystem.
3. `swapon --show` confirms which swap devices/files are currently active and their
   sizes/priorities.
4. UUIDs don't shift across reboots the way `/dev/sdX` names can.
5. autofs mounts on-demand and unmounts after idle time, instead of always being
   mounted.

**Recall:**
1. `[Unit]` holds metadata and dependencies (Description, After, Requires, Wants);
   `[Service]` defines how to run it (Type, ExecStart, ExecStop, Restart, User,
   Group); `[Install]` defines how it hooks into targets (WantedBy).
2. `Type=oneshot` is used because the process is expected to run to completion and
   exit — with `Type=simple` (default), systemd treats the main process exiting as the
   service having stopped/failed rather than having "finished successfully," and
   dependent units wouldn't be able to correctly order around a run-once task.
3. `Wants=` is a soft dependency — if the wanted unit fails to start, it doesn't block
   the dependent unit. `Requires=` is a hard dependency — if the required unit fails,
   the dependent unit is also stopped/fails to start with it.
4. `daemon-reload` re-reads unit files from disk into systemd's in-memory
   representation; without it, systemd keeps using the old cached definition even
   though the file on disk changed.
5. The systemd-timer equivalent of cron's `*/15 * * * *` (every 15 minutes) is a
   `[Timer]` section using `OnUnitActiveSec=15min` (relative recurrence) — or an
   `OnCalendar=` expression evaluated every 15 minutes if an absolute schedule is
   preferred.
6. `systemctl enable` creates the symlink into the target's `.wants/` directory so the
   unit starts automatically at boot, but does not start it now; `systemctl start`
   starts the unit immediately but doesn't affect whether it starts at boot. They are
   independent (`enable --now` does both).

---

## Day 013 — Logging: journald Persistence, rsyslog, logrotate

**Warm-up:**
1. `Wants=` is a soft dependency (failure doesn't block); `Requires=` is a hard
   dependency (failure blocks the dependent unit).
2. Because systemd caches unit files in memory; editing the file on disk alone doesn't
   apply until systemd re-reads it via `daemon-reload`.
3. `enable` sets a unit to start at boot (symlinks into the target's wants dir);
   `start` starts it immediately, right now — independent of each other.
4. PV, VG, LV.
5. It means the paired service should be run again 15 minutes after the last time it
   became active (relative recurrence, not tied to a fixed wall-clock time).

**Recall:**
1. `Storage=volatile` keeps the journal in RAM only, lost on reboot; `Storage=
   persistent` writes it to `/var/log/journal/` on disk, so it survives reboots (and
   enables `journalctl -b -1` etc.).
2. Per-application logrotate configs live in `/etc/logrotate.d/<name>` (global
   defaults live in `/etc/logrotate.conf`).
3. `copytruncate` rotates a log by copying its current contents to the rotated file
   and then truncating the original in place, rather than renaming/replacing it — it's
   needed when the writing application can't be signaled to reopen its log file after
   a normal rotate/rename.
4. The recommended order: (1) `journalctl -u <unit> --since "10 min ago"` for the
   specific service, (2) `journalctl -p err -b` for system-wide errors this boot,
   (3) check app-specific flat logs in `/var/log/`, (4) correlate timestamps across
   sources.
5. `logrotate -d /etc/logrotate.d/<name>` performs a verbose dry run without actually
   rotating anything, so you can confirm the config targets the right files/behaves
   correctly before trusting the schedule.
6. journald forwards to rsyslog by default on RHEL/Fedora via `ForwardToSyslog=yes` —
   they're chained rather than competing; rsyslog still handles flat-file routing
   (`/var/log/messages`, `/var/log/secure`) and remote forwarding.

---

## Day 014 — REVIEW (Days 8–13)

**Cumulative quiz:**
1. SIGTERM can be trapped/caught (allowing graceful cleanup); SIGKILL cannot. This
   matters because a well-behaved shutdown sequence should try SIGTERM first so
   processes can close files/flush data cleanly, escalating to SIGKILL only if a
   process is unresponsive.
2. `rpm -qf <path>` reports which installed package owns a given file — used for
   forensics, e.g. "where did this binary/config come from."
3. Repo definitions live in `/etc/yum.repos.d/*.repo`; `gpgcheck=1` protects against
   installing tampered or unsigned packages.
4. Because device names like `/dev/sdX` can shift across reboots (enumeration order
   isn't guaranteed), while a filesystem's UUID is stable — using it avoids mounting
   the wrong disk or failing to boot.
5. PV → VG → LV. Extend the LV before growing its filesystem, never the reverse.
6. `Wants=` is a soft dependency (its failure doesn't block the dependent unit);
   `Requires=` is a hard dependency (its failure does block/stop the dependent unit).
7. Because systemd only reads unit files into memory at reload time; without
   `daemon-reload` it keeps operating on the old cached unit definition.
8. A systemd timer integrates with journald logging, `systemctl status` visibility,
   and unit dependency ordering; cron is simpler for a one-off schedule but lacks that
   native integration.
9. `Storage=volatile` keeps the journal RAM-only (lost on reboot); `Storage=
   persistent` writes it to disk under `/var/log/journal/`, surviving reboots.
10. `copytruncate` copies the log's current content to a rotated file then truncates
    the original in place; it's required when the writer can't be told to reopen a
    freshly-renamed log file after rotation.

---

## Day 015 — Networking I: TCP/IP, Addressing, Subnetting, ip a/ip r

**Warm-up:**
1. `Wants=` is a soft dependency; `Requires=` is a hard dependency.
2. `Storage=persistent` makes journald write logs to disk (`/var/log/journal/`) so
   they survive reboot, instead of keeping them RAM-only.
3. `copytruncate` copies then truncates a log in place instead of renaming it — needed
   when the writing process can't reopen its log file after a rename-based rotation.
4. PV, VG, LV.
5. Narrow-to-specific: check the unit's own logs, then system-wide errors this boot,
   then app-specific flat logs, then correlate timestamps across sources.

**Recall:**
1. `/24` means the leading 24 bits are the network portion (mask 255.255.255.0),
   leaving 8 host bits = 256 total addresses, 254 usable (network and broadcast
   addresses reserved).
2. The three RFC 1918 private ranges: `10.0.0.0/8`, `172.16.0.0/12`,
   `192.168.0.0/16`.
3. ARP resolves an IP address to a MAC address on the local network segment; `ip
   neigh` shows the current ARP cache today (replacing the older `arp -a`).
4. `ip r get <dest>` tells you which route/interface the kernel would use to reach
   that destination, without actually sending any traffic.
5. Longest-prefix match means that among multiple matching routes, the one with the
   most specific (longest) subnet mask wins; a `/32` route (a single host) beats a
   `/24` route which beats the `default` (0.0.0.0/0) route for the same destination.
6. Link (Ethernet — MAC addresses), Internet (IP — routing), Transport (TCP/UDP —
   ports), Application (HTTP/SSH/DNS).

---

## Day 016 — Networking II: nmcli, DNS Client, /etc/hosts, ss

**Warm-up:**
1. A `/24` gives 254 usable hosts; splitting into `/26`s (4 subnets) gives 62 usable
   hosts each (64 addresses minus network and broadcast).
2. ARP resolves IP→MAC on the local segment; `ip neigh` shows the ARP cache.
3. Longest-prefix match: the most specific matching route (longest mask) wins.
4. Link, Internet, Transport, Application.
5. `ip r get <dest>` shows which route/interface would be used, without sending
   traffic.

**Recall:**
1. A "device" is the physical/virtual network interface itself; a "connection" is a
   saved configuration profile that can be applied to a device — one device can have
   multiple connection profiles, and nmcli manages connections, not devices directly.
2. `dig +short` returns just the terse answer (e.g. the IP), skipping the full
   QUESTION/ANSWER/AUTHORITY section detail that plain `dig` prints.
3. `dig @8.8.8.8 <domain>` queries a specific DNS server directly, bypassing your
   local resolver configuration — if the answer differs from your default resolver's
   answer, it isolates whether the problem is your local DNS config/cache or the
   actual DNS record itself.
4. `/etc/hosts` is checked first by default, before DNS; the order is configured via
   `/etc/nsswitch.conf` (the `hosts:` line).
5. `-t` = TCP sockets, `-u` = UDP sockets, `-l` = listening sockets only, `-p` = show
   the owning process, `-n` = numeric output (skip reverse DNS lookups).
6. `ss` replaced `netstat`; `ip` (specifically `ip a`/`ip link`) replaced `ifconfig`.

---

## Day 017 — Networking III: SSH Deep, Tunnels, scp/rsync

**Warm-up:**
1. `dig +short` gives just the terse answer, without the full query/answer/authority
   sections.
2. `/etc/hosts` is checked first by default, before DNS.
3. `-t` TCP, `-u` UDP, `-l` listening only, `-p` owning process, `-n` numeric (skip DNS
   lookups).
4. A device is the interface; a connection is a saved config profile applied to it —
   one device can have multiple profiles.
5. Longest-prefix match: the most specific route wins among multiple matches.

**Recall:**
1. If password auth is disabled before key login is confirmed working, and the key
   login is actually broken (misconfigured key, wrong permissions, etc.), you can be
   completely locked out of the server with no way back in.
2. `ssh-agent` holds decrypted private keys in memory for the session, so you don't
   have to retype your passphrase on every single connection — without it, an
   encrypted key still prompts for its passphrase each time it's used.
3. `sshd -t` syntax-checks the config before it's applied; reloading a broken config
   directly could break sshd entirely and lock out all future SSH access (including
   yours) until someone fixes it via console/out-of-band access.
4. `rsync -avz` only transfers the actual **differences** (delta) between source and
   destination on a second run, so an unchanged/already-synced tree transfers almost
   nothing; `scp` always copies the entire file(s) regardless of what's already there.
5. It sets up a **local port forward**: connections to your local port 8080 are
   tunneled through SSH to `localhost:80` as seen from the remote host — useful for
   securely reaching a remote-only service (e.g. an internal web UI) without exposing
   it directly.
6. A trailing slash on rsync's source (`source/`) copies the **contents** of that
   directory into the destination; no trailing slash (`source`) copies the directory
   itself (as a subdirectory) into the destination — a common source of "why did it
   nest an extra folder" surprises.

---

## Day 018 — firewalld: Zones/Services/Ports; SELinux Intro

**Warm-up:**
1. If key login isn't actually confirmed working and you disable password auth
   anyway, a broken key setup would lock you out entirely.
2. `sshd -t` syntax-checks the sshd configuration before you reload/restart it.
3. On a second run, `rsync -avz` transfers only the changed differences (delta),
   whereas `scp` would re-copy everything again.
4. It creates a local port forward, tunneling a local port through SSH to a
   host:port reachable from the remote side.
5. `ss` replaced `netstat`.

**Recall:**
1. A runtime change to firewalld applies immediately but is lost on reload/reboot; a
   permanent change (`--permanent`) persists across reload/reboot but only takes
   effect live once you run `firewall-cmd --reload`.
2. A rich rule is needed when you need conditional logic beyond a flat allow — e.g.
   restricting a service to a specific source subnet, logging, or rate limiting —
   which a plain `--add-service` (blanket allow for all sources) can't express.
3. `firewall-cmd --list-all` shows the complete picture of the active zone in one
   shot: interfaces, enabled services, open ports, and any rich rules.
4. SELinux's three modes are Enforcing, Permissive, and Disabled; Permissive is
   safest for troubleshooting since it logs would-be denials without actually
   blocking anything, while keeping the policy machinery active (unlike Disabled).
5. The **type** field of the SELinux context (`user:role:type:level`) drives most
   policy decisions.
6. Disabling SELinux entirely removes all its protection and hides real
   misconfigurations rather than surfacing/fixing them; Permissive mode keeps you
   informed of what SELinux *would* have blocked (via logs) while not actually
   enforcing, which is safer for diagnosis without losing visibility.

---

## Day 019 — SELinux Deep: Booleans, fcontext/restorecon, audit2why

**Warm-up:**
1. A runtime firewalld change applies now but doesn't survive reload/reboot; a
   permanent change persists but needs a `--reload` to take effect live.
2. Enforcing, Permissive, Disabled.
3. The type field of the SELinux context.
4. A rich rule is needed for conditional logic (e.g. source-restricted access, rate
   limiting) that a plain `--add-service` can't express.
5. The complete configuration of the active zone: interfaces, services, ports, rich
   rules.

**Recall:**
1. DAC (standard permissions) is discretionary — the file's owner decides who gets
   access. MAC (SELinux) is mandatory — access is constrained by system-wide policy
   that even root cannot simply override by virtue of being root.
2. `chcon` changes a file's context immediately but is not persistent — a
   `restorecon` run or a full filesystem relabel will revert it back to whatever the
   defined policy says it should be. `semanage fcontext` + `restorecon` defines the
   context rule persistently (in policy) and then applies it, so it survives future
   relabels.
3. `audit2why` translates raw SELinux denial log entries (from `/var/log/audit/
   audit.log`, typically piped from `ausearch -m avc`) into a plain-English
   explanation and often suggests the fix (a boolean or fcontext change).
4. Check DAC permissions first, then confirm the service is actually running/
   listening, then check firewalld, and only then suspect SELinux — SELinux is
   usually the last thing to blame, though often the actual cause once the other
   layers check out.
5. `audit2allow -M` can generate a custom policy module that over-grants permissions
   beyond what's actually needed to fix the specific denial, potentially opening up
   unintended access — it should be a last resort, ideally reviewed/signed off before
   applying.
6. `setsebool <bool> on` changes the boolean for the current session only (lost on
   reboot); `setsebool -P <bool> on` makes the change persistent across reboots.

---

## Day 020 — Web Serving Basics: nginx, curl -v, HTTP, TLS Concepts

**Warm-up:**
1. DAC is discretionary (owner decides access); MAC (SELinux) is mandatory
   (system-wide policy constrains even root).
2. `chcon` isn't durable because it's not tracked in policy — a `restorecon` run or
   relabel reverts it; `semanage fcontext` + `restorecon` persists the rule in policy
   first, then applies it, so it survives relabels.
3. `audit2why` reads SELinux denials from the audit log (`/var/log/audit/audit.log`)
   and produces a plain-English explanation of the denial.
4. DAC (permissions) → confirm running/listening → firewall → SELinux (last, though
   often the real cause).
5. `setsebool` changes a boolean for the current session only; `setsebool -P` makes it
   persistent across reboots.

**Recall:**
1. `nginx -t` checks the configuration file's syntax for errors; running it before
   `reload` avoids applying a broken config that could take the running service down.
2. `reload` gracefully applies configuration changes without dropping existing
   connections; `restart` fully stops and starts the service, briefly dropping
   connections.
3. In `curl -v` output, `>` lines show the request being sent (method, path, headers);
   `<` lines show the response received (status line, headers).
4. Examples: 2xx — 200 OK (success); 3xx — 301 Moved Permanently (permanent redirect);
   4xx — 404 Not Found (resource doesn't exist); 5xx — 500 Internal Server Error
   (server-side failure). (Any one valid example per family satisfies the question.)
5. A self-signed cert still encrypts traffic because the TLS handshake's encryption
   negotiation is independent of trust verification — the warning only flags that the
   certificate wasn't issued/signed by a recognized CA, not that the cipher/encryption
   itself is broken.
6. `/opt/fresher_project/www` needed `semanage fcontext` + `restorecon` because
   content under `/opt` isn't automatically labeled with the web-content SELinux type
   (`httpd_sys_content_t`) the way the default nginx docroot is — without the correct
   type, SELinux blocks nginx from reading it even though standard file permissions
   (DAC) are fine.

---

## Day 021 — REVIEW (Days 15–20)

**Cumulative quiz:**
1. `/24` means the leading 24 bits are network, giving 256 total addresses and 254
   usable hosts (network + broadcast reserved).
2. An nmcli "device" is the physical/virtual interface; a "connection" is a saved
   configuration profile applied to a device — one device can have several profiles.
3. `dig @8.8.8.8 <domain>` queries a specific server directly, isolating whether a DNS
   problem is in your local resolver/cache configuration or in the actual record
   itself.
4. Because if key-based login is actually broken and you've already disabled password
   auth, you can be locked out of the server entirely with no fallback.
5. A runtime change applies immediately but is lost on reload/reboot; a permanent
   change (`--permanent`) persists but requires `firewall-cmd --reload` to take effect
   live.
6. When you need conditional logic — e.g. restricting access to a specific source
   subnet, logging, or rate limiting — which a plain `--add-service` (unconditional
   allow) can't express.
7. DAC is discretionary access control decided by the file owner; MAC (SELinux) is
   mandatory access control enforced by system-wide policy that even root can't simply
   bypass.
8. `chcon` isn't tracked in policy, so a `restorecon` run or relabel reverts it;
   `semanage fcontext` defines the rule in policy first, and `restorecon` applies it,
   so the fix survives future relabels.
9. `>` lines are the request sent by the client; `<` lines are the response returned
   by the server.
10. Because the encryption/cipher negotiation in the TLS handshake is independent of
    the trust/identity verification step — a self-signed cert fails the trust check
    (no recognized CA signed it) but the traffic is still encrypted.

---

## Day 022 — Bash I: Variables, Quoting, test/[[ ]], if/case, Loops, read

**Warm-up:**
1. DAC = owner-decided discretionary access; MAC (SELinux) = mandatory, policy-enforced
   access that constrains even root.
2. Because `chcon` isn't recorded in SELinux policy — a later `restorecon` or full
   relabel reverts the file to whatever the defined policy context says.
3. `nginx -t` checks the configuration file for syntax errors before it's applied.
4. Runtime firewalld changes apply now but are lost on reload/reboot; permanent
   changes persist but need `firewall-cmd --reload` to take effect live.
5. Day 21's far-back check re-quizzed Day 1 (roughly the 21-day-back slot at that
   point in the course).

**Recall:**
1. `"$var"` prevents word-splitting and glob expansion of the variable's value — an
   unquoted `$var` containing spaces or glob characters can be split into multiple
   words or expanded unexpectedly (e.g. breaking on filenames with spaces).
2. `[ ]` (POSIX test) word-splits and glob-expands unquoted variables, which can cause
   syntax errors or wrong behavior; `[[ ]]` (bash extended test) does not word-split or
   glob-expand unquoted variables inside it, making it safer.
3. `-eq` performs a proper numeric comparison; `==` inside `[ ]` performs a string
   comparison, which can silently give a wrong (or coincidentally right) answer for
   numbers with different string representations (e.g. leading zeros) — `-eq` is
   explicit and correct for numeric intent.
4. `read -r` prevents backslash characters in the input from being interpreted as
   escape sequences (e.g. `\n`, `\t`) — without `-r`, literal backslashes in file
   content can be silently mangled.
5. `case` is preferable when matching one variable against several possible string
   values/patterns — it's cleaner and more readable than a long chain of `elif`
   comparisons against the same variable.
6. `$?` holds the exit status of the most recently executed command; check it
   immediately after the command you care about, since any intervening command
   (even `echo`) overwrites it.

---

## Day 023 — Bash II: Functions, Positional Args, Exit Codes, Arrays, Traps

**Warm-up:**
1. `"$var"` prevents unwanted word-splitting/glob-expansion of the variable's
   contents.
2. `[ ]` word-splits/glob-expands unquoted variables (risking bugs); `[[ ]]` does not.
3. `-eq` performs numeric comparison correctly; `==` in `[ ]` is a string comparison
   and can be misleading for numeric intent.
4. `read -r` stops backslashes in the input from being interpreted as escape
   sequences.
5. `case` is cleaner than `if/elif` when matching one variable against multiple
   distinct string values.

**Recall:**
1. `local` scopes a variable to the function it's declared in — without it, bash
   variables default to global scope, so a function could unintentionally overwrite a
   variable used elsewhere in the script.
2. `$?` gets overwritten by **every** subsequent command that runs (even something as
   innocuous as `echo`), so you must check it immediately after the command whose
   exit status you actually care about.
3. An indexed array is ordered and accessed by numeric position (`arr[0]`, `arr[1]`,
   ...); an associative array (bash 4+, `declare -A`) is a key-value map accessed by
   string keys.
4. `trap 'cmd' EXIT` guarantees the cleanup command runs regardless of *how* the
   script exits — normal completion, an error, or being killed by a signal (e.g.
   Ctrl-C) — whereas a plain cleanup line placed at the end of the script only runs if
   execution actually reaches that line (skipped on early exit or signal).
5. Loop over `"${!map[@]}"` (the `!` prefix on an array name expands to its keys/
   indices rather than its values).
6. `<<'EOF'` (quoted delimiter) disables variable/command expansion inside the
   here-doc, treating the content as fully literal text; `<<EOF` (unquoted) expands
   variables and command substitutions normally inside the block.

---

## Day 024 — Bash III: Robust Scripts, set -euo pipefail, shellcheck, getopts ★

**Warm-up:**
1. `local` scopes a variable to its function, preventing it from clobbering or being
   affected by a same-named variable elsewhere in the script (global by default
   otherwise).
2. Every subsequent command overwrites `$?`, so it must be checked immediately after
   the command being evaluated.
3. Indexed arrays use numeric positions; associative arrays (bash 4+) use string
   keys.
4. An EXIT trap guarantees the cleanup command runs on any exit path — normal, error,
   or signal-terminated — while a plain end-of-script cleanup line only runs if
   execution reaches it normally.
5. `read -r` prevents backslashes in input from being interpreted as escape
   sequences.

**Recall:**
1. `set -e` does not trigger inside conditions of `if`, `&&`, `||`, or `while` (and
   similar contexts) — commands there are expected to potentially "fail" as part of
   normal control flow, so a non-zero exit in those positions doesn't stop the script.
2. `set -u` catches references to unset variables (treating them as an error) that
   plain bash would otherwise silently expand to an empty string — this catches typos
   like `$fiel` instead of `$file` immediately rather than letting them fail silently
   downstream.
3. `pipefail` changes a pipeline's exit code to reflect failure if **any** stage of
   the pipeline fails, not just the last command — without it, only the last
   command's exit status is reported (e.g. `false | true` reports success).
4. SC2086 generally flags a variable used without double quotes, warning that it may
   be word-split or glob-expanded unexpectedly ("double quote to prevent globbing/
   word splitting").
5. In `getopts "o:vh"`, the colon after `o` means the `-o` option requires an
   accompanying argument value, which getopts places into `$OPTARG`.
6. Systemd timers were chosen over cron for the project because they integrate with
   journald logging, dependency ordering, and `systemctl status` visibility — giving
   better observability than a plain cron entry, even though cron is simpler for a
   one-off schedule.

---

## Day 025 — Python I: Install/venv, Syntax, Types, Control Flow, REPL

**Warm-up:**
1. `set -e` does not trigger inside conditions of `if`/`&&`/`||`/`while` — failures
   there are treated as expected control flow, not fatal errors.
2. `pipefail` makes a pipeline's exit status reflect failure if any stage fails, not
   just the last command.
3. shellcheck is a static analyzer that catches quoting bugs, unused variables, and
   other common bash mistakes before you consider a script "done."
4. The colon after `o` means `-o` requires an argument value (retrieved via
   `$OPTARG`).
5. Because systemd timers integrate with journald logging, dependency ordering, and
   `systemctl status` visibility, unlike plain cron.

**Recall:**
1. A venv isolates a project's dependencies from the system Python (and from other
   projects), avoiding version conflicts and protecting the system Python that OS
   tooling like `dnf` itself may depend on.
2. Indentation (whitespace) defines code blocks in Python instead of braces (PEP 8
   convention is 4 spaces; mixing tabs and spaces is an actual error).
3. Lists (`[1, 2, 3]`) are mutable; tuples (`(1, 2, 3)`) are immutable.
4. `"1" + 1` raises a `TypeError`, because Python is strongly typed and won't
   implicitly coerce a string and an int together for `+` the way bash's loose string
   concatenation would.
5. `subprocess.run([...])` returns a `CompletedProcess` object; its `.returncode`
   attribute (0 = success) tells you whether the command succeeded (`.stdout`/
   `.stderr` hold captured output if `capture_output=True`).
6. `&` (intersection), `|` (union), `-` (difference).

---

## Day 026 — Python II: Functions, Files/Context Managers, Exceptions, Idioms

**Warm-up:**
1. To avoid "works on my machine" version conflicts and to protect the system
   Python that OS tooling depends on.
2. Lists are mutable; tuples are not.
3. `TypeError`.
4. Whether the subprocess call succeeded — `.returncode == 0` means success.
5. `&` (the `&` operator on sets gives intersection).

**Recall:**
1. `with open(...) as f:` guarantees the file is closed automatically even if an
   exception occurs inside the block — a manual `open()`/`close()` pair would leave
   the file open if an exception happened before the explicit `close()` call.
2. Catching specific exceptions avoids accidentally swallowing things you didn't
   intend to catch (like `KeyboardInterrupt` or `SystemExit`, which a bare `except:`
   also catches), and makes error-handling intent explicit and debuggable.
3. `finally` — it guarantees a block of code runs regardless of whether the `try`
   succeeded or raised, the same guarantee bash's `trap ... EXIT` provides for
   scripts.
4. `dict.get(key, default)` avoids raising a `KeyError` when the key is missing,
   returning the given default instead.
5. `[x*2 for x in nums if x % 2 == 0]`.
6. `*args` arrives inside the function as a **tuple** of the extra positional
   arguments.

---

## Day 027 — Python III: Modules/pip, argparse, os/pathlib/subprocess; sys_report.py v0

**Warm-up:**
1. That the file is automatically closed even if an exception occurs inside the
   block.
2. Because a bare `except:` also silently catches things you likely didn't intend to
   catch (e.g. `KeyboardInterrupt`), hiding bugs and making debugging harder.
3. A `KeyError` on a missing key.
4. List comprehension: `[expr for item in iterable]`; dict comprehension: `{key_expr:
   val_expr for item in iterable}` (uses `{}` and a `key: value` pair per element).
5. Whether the subprocess call succeeded (`.returncode == 0` means success).

**Recall:**
1. A list of args avoids invoking a shell to parse the command string, eliminating
   shell-injection risk and the quoting/escaping headaches that come with building a
   single command string, especially with untrusted or variable input.
2. `pathlib`'s `/` operator joins path components (e.g. `Path("/opt") / "logs" /
   "file.log"`), which is more readable than string-based `os.path.join`.
3. `shutil.disk_usage(path)` returns a named tuple of `(total, used, free)` bytes for
   the filesystem containing that path — the Python equivalent of `df`.
4. argparse is Python's structured way to build a CLI (auto-generated `--help`, typed/
   named arguments, required vs optional), serving the same purpose bash's `getopts`
   serves for flag parsing in shell scripts.
5. So it runs anywhere without needing `pip install` — keeping it dependency-free
   makes the tool portable across any machine with a standard Python 3 install.
6. `os.getloadavg()` returns the 1/5/15-minute system load averages as a tuple; the
   `uptime` command shows the same numbers.

---

## Day 028 — REVIEW (Days 22–27)

**Cumulative quiz:**
1. Because unquoted `$var` is subject to word-splitting and glob expansion by the
   shell, which can break on values containing spaces, globs, or other special
   characters — quoting preserves the value as a single literal word.
2. `set -e` (exit on any command failure, except inside `if`/`&&`/`||`/`while`
   conditions), `set -u` (treat unset variables as errors instead of expanding to
   empty), and `set -o pipefail` (make a pipeline's exit code reflect failure of any
   stage, not just the last command) — combined as the standard `set -euo pipefail`
   header.
3. SC2086 flags a variable used without double quotes, warning it may be
   word-split or glob-expanded unexpectedly.
4. That `-o` requires an accompanying argument, captured in `$OPTARG`.
5. Lists are mutable; tuples are immutable.
6. It guarantees the file is closed automatically even if an exception is raised
   inside the block, unlike a manual `open()`/`close()` pair which could leave the
   file open if an error occurs before `close()` executes.
7. Because a bare `except:` also catches things you likely don't intend to handle
   (like `KeyboardInterrupt`), which can hide bugs and make intent unclear.
8. A list of args avoids invoking a shell to interpret the command, eliminating
   shell-injection risk and manual quoting/escaping concerns.
9. It joins path components (e.g. `Path("/opt") / "logs"`), a more readable
   alternative to `os.path.join`.
10. `sys_report.py` captures: CPU load (`cpu_load` — 1/5/15-min averages), memory
    (`memory` — total/used/available in kB), disk usage of root (`disk_root` —
    total/used/free bytes), and boot-time error count (`boot_errors` — count of
    `journalctl -b -p err` lines).

---

## Day 029 — RHCSA-Style Mock Skills Day (Days 1–27)

**Warm-up:**
1. The four domains tested: users/permissions, storage (including LVM), systemd, and
   networking-and-SELinux (combined with firewalld).
2. Day 11 (Storage II: LVM) covered the LV-then-filesystem extend order.
3. Day 19 (SELinux Deep) covered `semanage fcontext` + `restorecon`.
4. Day 12 (systemd Deep) introduced systemd timers.
5. Day 24 (Bash III) covered `set -euo pipefail`.

**Recall:** Day 29 has no separate "Recall questions" section — it is a timed mock
assessment with tasks and a self-grading checklist instead. The 6 timed tasks and
self-grading checklist (from the day file) function as this day's assessment:
- Task 1 (Users & permissions): create `rhcsa_mock` user/`mockgrp` group, 60-day
  password expiry via `chage`, `/srv/mockdata` at 2770 SGID owned `root:mockgrp`,
  confirm group inheritance on new files.
- Task 2 (Storage): partition + xfs format a spare/loopback disk, persistent mount at
  `/mnt/mockstorage` via UUID with `nofail`, verified with `mount -a`.
- Task 3 (LVM): `mock_vg` from a PV, 300M `mock_lv` formatted ext4, mounted at
  `/mnt/mocklv`, extended by 200M with `resize2fs` grown live.
- Task 4 (systemd): oneshot `.service` running `/usr/bin/date >> /tmp/mock_date.log`
  paired with a 5-minute `.timer`, enabled/started, confirmed via `systemctl
  list-timers` and a manual trigger.
- Task 5 (Networking/firewalld): hand-calculate the 3rd `/26` subnet of
  `192.168.50.0/24` (network `192.168.50.128/26`, usable range
  `192.168.50.129`–`192.168.50.190`, broadcast `192.168.50.191`); open `8443/tcp`
  persistently, confirm with `firewall-cmd --list-ports`.
- Task 6 (SELinux): `/srv/mockweb` + `index.html`, point nginx `root` at it, fix the
  403 with `semanage fcontext` + `restorecon` (not `chcon`), confirm 200 via `curl`.

---

## Day 030 — ★ Phase 1 Capstone: The system_report Tool

**Warm-up:**
1. `sys_report.py` currently captures: CPU load (1/5/15-min averages), memory (total/
   used/available), disk usage of `/`, and boot-time error line count.
2. Task 6 (SELinux) in Day 29's mock covered `semanage fcontext` + `restorecon`.
3. `resize2fs` grows (or shrinks, offline) an ext4 filesystem to match its underlying
   block device/LV size; it must run **after** `lvextend`, never before, since the
   filesystem can't be grown beyond the space actually allocated to the LV.
4. `systemctl enable` sets a unit to start at boot (symlinks it into the target's
   wants directory) but doesn't start it now; `systemctl start` starts it immediately
   without affecting its boot-time behavior.
5. The four RHCSA-mock domains: users/permissions, storage (including LVM), systemd,
   and networking-and-SELinux/firewalld.

**Recall:**
1. Collector (`boot_logger.sh`, bash — gathers boot/systemd/log facts and writes text
   logs), Formatter (`sys_report.py`, Python — produces structured JSON of CPU/memory/
   disk/error data), Presentation (the HTML report combining both, served by nginx).
2. The formatter lives in Python because Python's data structures and native JSON
   support handle structured numeric data more cleanly than bash, which is clumsy at
   anything beyond text/line processing.
3. Permissions (DAC — `svc_fresher` must be able to run the scripts and write to
   `www/`), SELinux (nginx must have correct context to read `www/` content), and
   firewalld (must allow HTTP/HTTPS traffic through) all had to agree simultaneously.
4. At minimum: what the tool does, how to run it manually, how the automation
   (systemd timer) works, where the output lives, and a note on how to extend it.
5. `systemctl list-timers` (checked after a reboot) proves the timer re-armed and the
   automation survives a reboot without manual intervention.
6. `sys_report.json` lives in `/opt/fresher_project/logs/`; the HTML report embeds its
   pretty-printed contents alongside the bash log tail in `report.html`.
