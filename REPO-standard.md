# REPO.md standard

## Purpose

Every active repo in `revereveal` has a `REPO.md` at its root. The file answers
four questions in 10 seconds of reading:

1. **What is this?** — elevator pitch, 1 sentence
2. **What uses it?** — consumers (other repos, live URLs, external)
3. **What does it use?** — producers (other repos, services, data sources)
4. **What platform does it belong to?** — commercial / kernel / runtime / peripheral

`REPO.md` is distinct from `README.md` (which is external-facing documentation
for potential users). `REPO.md` is internal positioning — for James, for future
collaborators, and for Claude-Code agents doing cross-repo reasoning.

## Required format

```markdown
# <repo-name>

**Platform:** Commercial | Kernel | Runtime-CLS | Peripheral  
**Status:** Active | Maintenance | Attic-candidate | Archived  
**Role:** <one sentence — what this repo IS, not what it does>

## Consumers (what uses this)
- <live URL or repo name> — <1 line why>
- ...

## Producers (what this uses)
- <repo name, service, data source> — <1 line why>
- ...

## Live surfaces (if any)
| Surface | URL | What |
|---|---|---|
| ... | ... | ... |

## Key contacts / entry points
- <file path>:<line> — <what's there>
- ...

## Status notes
- <1-3 bullet points about current state, known risks, planned work>
```

## Rules

- **One-page max.** If you can't say it in a page, simplify. Detail goes in README.md or docs/.
- **Maintain when status changes.** Not optional. An outdated REPO.md is worse than none.
- **No aspirational text.** Only what exists now. Future plans go in docs/plans/ or GitHub issues.
- **Cross-link by repo name, not URL.** Use `revasser-operating-system` not full https://github.com URL — makes the file survive org renames.
- **If any section is empty, say so explicitly.** "No external consumers" is useful signal; missing section is ambiguous.

## Validation

Phase 4 governance includes a GitHub Action that:
- Checks every repo has `REPO.md` at root
- Warns if REPO.md hasn't been modified in >6 months (stale signal)
- Does not block merges — lint-only

## Example: filled REPO.md for `grant-pipeline`

```markdown
# grant-pipeline

**Platform:** Commercial (funding ops)  
**Status:** Active  
**Role:** Grant and credits pipeline across Azure, Anthropic, OpenAI, AWS, Microsoft — tracks deadlines, drafts applications, reconciles received credits.

## Consumers (what uses this)
- James (operator, primary) — daily-brief emits digest to Slack #grants
- `revasser-operating-system` — reads grant state for commercial narrative
- `portfolio/hub` — surfaces active grant tracks in cockpit

## Producers (what this uses)
- Azure ISV Success portal — credit application flow
- Anthropic Startup Program — application draft state
- AWS Activate Founders — submission state
- OpenAI Researcher Access — 44-day window tracking
- Google Grants Guidebook API — opportunity ingestion

## Live surfaces
| Surface | URL | What |
|---|---|---|
| Daily brief | `#grants` Slack channel | Posted by agent-runner |
| Agent endpoint | api.revasser.ai/v1/operator/agent/grants | Callable query surface |

## Key contacts / entry points
- `agent/ARCHITECTURE.md` — mermaid diagram + operational notes
- `capability-map/hunt.md` — grants being actively pursued
- `capability-map/can.md` — grants won / in-flight
- `plan.md` — top-level strategy (8 tracks, $130K+ potential)
- `shared/cross-domain-narrative.md` — unified pitch text reused across applications

## Status notes
- 2026-04-17: AWS Activate Founders still DRAFT READY unsubmitted
- 2026-04-17: Anthropic Fellows 04-26 deadline surfaced (9d window)
- Azure Investor Network framing conflict surfaced 2026-04-17 — review before submit
- OneDrive mirror restored 2026-04-17 (8/8 briefs under /Grants/2026-04-17/)
```
