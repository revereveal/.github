# Solo-dev GitHub operations posture

This org is operated by one person. GitHub automation should reduce cognitive load, not create notification debt.

## Defaults

- Prefer workflows that are cheap, deterministic, and quiet.
- Prefer inline scripts or checked-in local actions over third-party actions when the job is small.
- When a third-party action is necessary, pin it to a full commit SHA because org policy requires SHA pins.
- Avoid scheduled workflows that create noisy issues or failed-run notifications unless they produce a clear next action.
- Treat stale failure notifications as stale evidence. Check the latest run before spending time fixing an old run.

## PR and dependency queue rules

- Approve/merge only low-risk PRs with green checks and an obvious blast radius.
- Do not merge failing Dependabot PRs individually when the failure indicates a coupled dependency lane.
- Bundle coupled dependency updates into one deliberate sweep when peer dependencies or runtime versions are involved.
- Prefer one aggregate required check over many separate required checks.

## Rêvasser commerce rules

- Keep public landing stable unless the task explicitly targets it.
- Keep commerce gated/private until preview signoff.
- PR preflight should validate source/theme readiness; full runtime release gates belong on manual release dispatch or a deliberate release branch.

## Cost and accessibility rule

Every workflow should answer: does this save the solo operator time this week? If not, disable it, make it manual, or replace it with a smaller checked-in script.
