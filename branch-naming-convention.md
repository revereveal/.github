# Branch naming convention

Purpose: make branch intent scannable without opening it. One prefix per branch.
The orphan-branch sweep (runs weekly) interprets prefixes to decide what to warn about.

## The five prefixes

| Prefix | Meaning | Orphan-sweep behavior | Expected lifetime |
|---|---|---|---|
| `feat/` | Feature work intended to merge | Reminder after 14 days with no PR | Days to weeks |
| `fix/` | Bug fix intended to merge | Reminder after 14 days with no PR | Days |
| `spike/` | Exploratory work — may be thrown away | Reminder after 30 days (longer leash) | Weeks |
| `salvage/` | Recovery of nearly-lost work from a stash, reflog, or dropped commit | Reminder after 30 days (longer leash) | Variable |
| `archive/` | **Not intended to merge.** Preserved for historical record. | No reminders (excluded from sweep) | Permanent |
| `release/` | Release prep branch (e.g., `release/v4.1.2`) | Reminder after 14 days if unshipped | Short |
| `codex/`, `claude/`, `copilot/` | Agent-generated branches (from Codex, Claude Code, GitHub Copilot) | Same as `feat/` | Days to weeks |

## Rules

1. **Every branch gets a prefix.** No exceptions except `main` and `master`.
2. **Prefix is permanent for the branch's life** — don't rename `feat/foo` → `archive/foo` mid-life. Instead: open a PR explaining archival, merge or close, then rename.
3. **Archive is real** — using `archive/` means you're committing to never merge. The GitHub branch stays forever unless explicitly deleted.
4. **Date-suffixed names are okay** — `archive/otter-drive-sync-automation-2025-07` is clearer than `archive/otter-drive-sync` when multiple archived branches might compete for the same name.
5. **Agent prefixes (`codex/`, `claude/`) may be renamed** — if an agent-generated branch graduates into a deliberate feature, rename `codex/matter-ops-hub-recovery` → `feat/matter-ops-hub-recovery` on PR-open.

## What goes in `archive/` (examples from the 2026-04-18 audit)

- `archive/otter-drive-sync-automation-2025-07` — pre-Kernel era, superseded by pivot
- `archive/user-scoped-foundation-2025-07` — same era
- `archive/deep-blast-radius-fixes-2026-03` — cross-repo effort abandoned in both codex-otter-automation and revasser-site-build

## What should NOT be in `archive/`

- A feature branch you might come back to → keep as `feat/` and let the sweep remind you
- A fix that partially works → `fix/` + add a comment in PR about known gaps
- A spike that produced learnings → document the learnings, delete the branch. Don't archive spikes that have no code worth preserving.

## Interaction with attic

`portfolio/attic/` is for **retired products** (whole repos, subtrees). `archive/` branches are for **retired work within an active repo**. A repo can have `archive/` branches but not be in attic. A repo in attic shouldn't have any active branches at all (delete or archive them before moving to attic).

## Enforcement

- No hard enforcement — just discipline + the orphan-branch sweep reminder
- Future-consideration: a GitHub Action that auto-comments on branch creation if the prefix is missing. Not worth building yet for solo dev.
