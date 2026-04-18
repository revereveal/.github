# Anti-recommendations

What I should NOT set up, and why. Solo-dev guardrails.

The positive recommendations in Phase 4 are half the story. This file is the other half. Every time you're tempted to add one of these, re-read this file.

## Tooling NOT to add

### 1. Required reviewers on branch protection

**Don't:** Set `required_approving_review_count: 1` on the branch ruleset.

**Why:** You're the only human reviewer. Requiring reviews means either you approve your own PRs (meaningless gate) or you block your own merges (workflow-breaking). Either way it's theater.

**When this changes:** invite a collaborator for a real engagement (not a contractor running a one-off script). At that point, set required_approving_review_count=1 scoped to repos they actually work in.

### 2. Daily-cadence Dependabot

**Don't:** Set dependabot schedule to daily.

**Why:** You have 15+ active repos. Daily = ~75 PRs/week. You will lose context. Weekly is the right cadence for solo operations; critical security updates ride the security-updates channel and come through on any cadence anyway.

**When this changes:** never. Even at team-scale, weekly+grouped is correct for most workflows.

### 3. Jira / Linear / monday.com integration

**Don't:** Connect GitHub to any external project management tool.

**Why:** Asana is the current external project tool (per existing skill `asana-ops`). Adding Jira would fragment. GitHub Issues is the right place for repo-scoped work; Asana for cross-repo project work; dual-tracking creates reconciliation overhead you won't maintain.

**When this changes:** if a collaborator joins who has muscle memory for Linear and builds the integration themselves.

### 4. Auto-formatters in CI that block merges

**Don't:** Add `prettier --check` or `black --check` as required CI.

**Why:** You ship code via Claude Code and via Codex agents. Both auto-format during generation. CI enforcement of formatting blocks merges when the agent-produced code is correct but differs in a semicolon placement. Better: run formatter as a pre-commit hook locally (which you already have), and let CI focus on tests.

**When this changes:** if two+ humans contribute code directly without agent mediation.

### 5. Required status check: `license-check`

**Don't:** Add a license-scanner required check.

**Why:** Your dep tree is small, well-known, and under MIT/Apache/BSD. A false-positive from a license scanner would block merges for no real-world benefit. If you ever consume a proprietary dep, you'd know about it at install-time, not via a post-hoc scanner.

**When this changes:** if you raise outside capital or take on an enterprise customer with legal/procurement review requirements.

### 6. Per-PR automatic deployment

**Don't:** Deploy to staging on every PR.

**Why:** Solo work means you deploy to prod when it's ready. Staging deploys per-PR burn Cloudflare CPU time budget and Pages build minutes for no validation benefit beyond what your local dev + test suite already gives. Deploy on merge-to-main only.

**When this changes:** if a collaborator needs to review a live version of a feature that can't be validated on localhost.

### 7. GitHub Projects beta boards for every repo

**Don't:** Create a GitHub Projects board per repo.

**Why:** Most of your repos have 0-2 active issues at any time. A project board is overhead for tracking nothing. The cross-repo project (`Revasser Runtime Platform — Phase 1`, Project #3) IS worth maintaining because it aggregates. Per-repo boards are not.

**When this changes:** a specific repo exceeds 10 open issues and you need to triage by priority/status. At that point, create a board for THAT repo only.

### 8. CodeQL / SAST scanning on all repos

**Don't:** Enable CodeQL across all 35 active repos.

**Why:** CodeQL is valuable for public repos exposed to external contributors (supply-chain attack surface) and for compliance-regulated code. Your fleet is primarily private, solo-authored, and not in a regulated domain. False-positive triage time would outweigh signal.

**When this changes:** for any repo that goes public with community contributions (`slack-mcp-server` is the current example — worth enabling there specifically).

### 9. Org-level secret scanning with push-protection on every repo

**Don't:** Turn on push-protection for secret scanning org-wide.

**Why:** Cloudflare API keys, Google ADC paths, and other credentials appear in your docs for operational reasons — they're scoped credentials, not production secrets. Push-protection would block documentation commits. Secret scanning (detection-only) is fine; push-protection creates friction.

**When this changes:** never for docs-containing repos. Worth enabling for public repos specifically (e.g., `slack-mcp-server`).

### 10. Mandatory PR templates

**Don't:** Add `.github/pull_request_template.md` with required checkboxes.

**Why:** Solo dev. You already know what the PR does. A template adds ceremony without accountability enforcement. The structured-commit practice (scoped commits with conventional-commit prefixes) provides the same signal.

**When this changes:** when an external contributor submits a PR for the first time. At that point, add a contributor-facing template.

## Processes NOT to adopt

### 11. Weekly standups with yourself

**Don't:** Schedule a recurring "solo standup" via calendar.

**Why:** You already have daily-brief emission from `grant-pipeline` agent and the Mini nonstop executor producing continuous state. Adding a meeting-shaped artifact doesn't add info density.

### 12. "Definition of Done" as a formal doc

**Don't:** Write a team-style "DoD" checklist.

**Why:** Solo context = DoD is whatever ships + makes revenue or solves the problem. Codifying it freezes the definition; the definition should flex per-commit.

### 13. Multi-stage environments (dev/staging/prod/canary)

**Don't:** Introduce staging or canary environments for each product.

**Why:** Cloudflare Workers have preview environments built-in. For most work, localhost + Cloudflare preview + main-deploys-to-prod is enough. Canary is a team-scale concept designed for rollback safety at scale; at your scale, you can revert a bad deploy in 60 seconds via `wrangler rollback`.

**When this changes:** for any product with paying customers where a bad deploy is visible to them. `slack-mcp-hosted` is the first candidate; but even there, a 30-second revert is enough today.

### 14. SLAs and SLOs on internal services

**Don't:** Publish uptime/latency SLAs for internal services.

**Why:** The services are used by one operator (you). You know when they're broken because you hit them. Formalizing SLAs adds measurement overhead without delivery commitment.

**When this changes:** when a paying customer uses a service directly (not currently the case for any listed product).

## Repo structure NOT to impose

### 15. Monorepo everywhere

**Don't:** Pull everything into `portfolio` as one monorepo.

**Why:** Monorepo requires mature tooling (bazel, pants, nx) to work at scale. Your distributed repos have distinct CI needs and distinct lifecycles. The proposed consolidations (`usps-v3` trilogy → monorepo; `home-diary-ocr-lab` → home-diary subdir; attic retirement subtrees) are targeted where sync cost is real.

**When this changes:** if and only if tooling lands that makes cross-repo refactor ergonomic (shared TypeScript types, shared deploy pipeline). Not today.

### 16. Language-specific monorepos

**Don't:** Make "all Python services" one repo and "all TS services" another.

**Why:** Language is an implementation choice, not a product boundary. Your commercial-ops + kernel + runtime platforms span languages intentionally.

### 17. Documentation repos separate from code

**Don't:** Create `revasser-docs` as a standalone repo.

**Why:** `north-star` already holds architectural narrative. Per-repo READMEs + REPO.md + `docs/` subdirs hold the rest. Separating docs from code invites drift.

---

## The meta anti-recommendation

**Don't add governance because the audit said to.** Everything in Phase 4 is small enough that you can adopt each piece independently. If any piece creates friction, drop that piece. Solo-dev governance is a dial, not a binary.

The one exception: **do keep the orphan-branch sweep running.** Even if you ignore every other artifact, the weekly reminder prevents the specific failure mode that created this audit in the first place — 114-commit PRs sitting open for 9 days because nobody looks at branches.
