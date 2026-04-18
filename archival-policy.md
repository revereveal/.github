# Archival policy

Three retirement mechanisms. Pick one based on what you're retiring.

## Decision tree

```
Is the whole repo retired?
├── YES — has substantive content worth reading in future?
│   ├── YES → attic (move to portfolio/attic/)
│   └── NO — is there anything that could cause real harm if deleted (external refs, billing, legal)?
│       ├── YES → archive (GitHub archive flag on standalone repo)
│       └── NO  → delete (git remote + repo)
│
└── NO — retiring a branch/feature/module within a repo
    ├── Might come back → archive/<name> branch prefix (stays in repo)
    └── Won't come back → delete the branch (after subtree-preserving if needed)
```

## Mechanism 1: `attic` (portfolio/attic/)

**Use when:** retiring a whole repo with substantive content — retired commercial
product, abandoned experiment with working code, tied to external infra that needs
documentation.

**What happens:**
1. Document infrastructure ties in the retired repo's `INFRA-TIES.md`
2. Subtree-merge into `portfolio/attic/<name>/` preserving history
3. Delete the standalone repo
4. Update `portfolio/attic/README.md` with new entry

**Cost:** 30-60 minutes per repo (infra docs + subtree merge + README update)

**Examples (Phase 3 decisions):** `xlsx-fixer-pro`, `revasser-notes-retrieval`, `web-hosting-deployme`, `revasser-ops-fleet`, `homebrew-claude`

## Mechanism 2: `archive` (GitHub archive flag)

**Use when:** the repo must stay accessible at its original URL (external links, published npm package, contract references) but no further development is planned.

**What happens:**
1. Set archive flag: `gh repo archive revereveal/<name>`
2. Repo becomes read-only; no issues/PRs/pushes
3. URL stays the same; clones still work
4. Remove from CI/CD inclusion lists

**Cost:** 5 minutes

**Examples:** none yet in this org — but anything with an external npm/pip package or customer-facing URL would qualify.

**Difference from attic:** archive preserves the standalone URL; attic consolidates into portfolio.

## Mechanism 3: `delete`

**Use when:** zero substance, zero references, zero harm.

**What happens:**
1. Sanity check: `grep -rn '<repo-name>' ~/Developer/GitHub/` for any cross-repo reference
2. Delete: `gh repo delete revereveal/<name>` (requires confirmation)
3. GitHub preserves the name as "unavailable" for ~60 days (anti-squat)

**Cost:** 2 minutes

**Examples (Phase 3 decisions):** `claude-daemon`, `jta/grant-pipeline`, `jta/xlsx-fixer`, 4× `claw-code-*`, `jta/awesome-mcp-servers-1`

## Branch-level retirement

Separate from repo retirement. Handled by the [branch-naming convention](./branch-naming-convention.md):

- `archive/<name>` prefix — branch stays in repo forever, no orphan reminders
- Delete the branch — nothing recoverable, clean slate

## What triggers retirement

Retirement is a conscious decision, not a schedule. But these signals suggest it's time:

- Repo with <5 commits and no activity in 90+ days → candidate for `delete`
- Repo with substantive content and no activity in 180+ days, no live URL → candidate for `attic`
- Repo with live URL/package but no development in 180+ days → candidate for `archive`
- Repo referenced by another retired repo (transitive dependency) → retire together

## What the archived/atticked README MUST say

Every retired repo's top-level README must lead with:

```markdown
# <repo-name> — RETIRED

**Retired:** YYYY-MM-DD  
**Why:** <one sentence>  
**Replaced by:** <pointer, or "N/A — product sunset">  
**Do not:** push to this repo, file issues, expect responses

Historical documentation follows below.
```

This is load-bearing. Future-you or a collaborator will thank you for not
leaving ambiguous-state repos.

## What the retirement must NOT do

- Do not delete contributing history when atticing — always subtree-preserve.
- Do not unlist repos without archiving — silent disappearance breaks bookmarks.
- Do not retire infrastructure repos mid-deploy. Wait for a clean cycle.
- Do not retire repos that other active repos import from, until imports are migrated.

## Audit cadence

- **Quarterly:** run the portfolio audit playbook (this document's siblings in Phase 1-3). Takes ~1 hour with current tooling.
- **Weekly:** the orphan-branch sweep runs automatically. No manual cadence needed.
- **Ad-hoc:** when a repo crosses the signals above, retire immediately. Don't batch.
