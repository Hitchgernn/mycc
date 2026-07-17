---
name: commit-skill-agy
description: Auto-triggers after self-contained changes to inspect diff, verify, write Conventional Commit, and commit via isolated subagent context.
---
# Auto-Commit Workflow

Skill auto-triggers after small, self-contained change (single fix, single feature step, single file edit). No explicit prompt needed.

**Model / Subagent Note:**
Static agent config files not supported. Subagents require dynamic spawn via `define_subagent` and `invoke_subagent`. Model pinning not supported in frontmatter or subagent API. Subagents inherit main session model. Context isolation works (spawns separate context), but model remains Gemini 3.1 Pro (High).

## Procedure

Main thread spawns subagent for isolation:

1. **Inspect:** Run `git status`, `git diff --stat`, `git diff --cached --stat`. Read full diff. Base message on actual changes.
2. **Screen:** Flag and stop if secrets found (`.env`, `*.pem`, `*.key`, `id_rsa`, tokens, passwords). Exclude noise (`node_modules/`, logs, caches, build, OS/editor files). Stage explicitly (`git add <path>`). No blanket add.
3. **Split:** If diff mixes unrelated concerns, propose split (files + title per group). Wait for user confirm.
4. **Verify:** Check if fast build/typecheck/lint/test passed. Run if cheap. Do not commit broken code unless WIP checkpoint requested explicitly.
5. **Write and commit.** Use the format below. Show the title before committing unless the user has said to just commit.

## Format

Default — title only:

```txt
type(scope): short summary
```

- `type(scope): summary` — lowercase, imperative, ≤ ~70 chars; `scope` optional.
- No body, no bullets, no trailing period.

Detailed (only when explicitly requested) — title, one blank line, then concise bullets:

```txt
type(scope): short summary

- area: detail
- area: detail
```

In a detailed body, call out anything a reviewer must know: API/behavior or breaking changes, security/privacy, migrations, dependency bumps.

## Types

`feat` new behavior · `fix` bug fix · `refactor` no behavior change · `perf` performance · `docs` docs · `test` tests · `build` build system/deps · `ci` CI config · `chore` tooling/housekeeping · `style` formatting only.

## Scope

Infer from what's touched — package, module, directory, or feature. Follow the repo's own conventions (check `git log --oneline -10`). Omit the scope when it adds nothing; for a repo-wide change use the broadest accurate scope (e.g. `repo`).

## Rules

- Conventional format exactly: `type(scope): summary` — never `type:(scope):` or `type(scope) :`.
- Title only by default; never add a body, bullets, or changelog details unless explicitly asked.
- Match the repo's existing commit style.
- No emojis, no trailing period on the title, no markdown headings in the body.
- Don't add `Co-Authored-By` / attribution trailers unless the project clearly uses them.
- Don't use `git commit -a`, amend, force-push, or rewrite history unless asked.
- Describe what changed and why from the reader's side — not "updated files".

## Examples

Default (title only):

```txt
feat(frontend): migrate dashboard map to Leaflet
```

```txt
fix(parser): handle empty input without throwing
```

```txt
chore(repo): ignore build artifacts
```

### Proposing a split (step 3)

When the diff mixes unrelated work, present it like this and wait for confirmation:

```txt
This diff mixes two unrelated changes — I'd split it:

1. fix(api): reject negative page sizes
   - src/api/pagination.ts
2. chore(deps): bump eslint to v9
   - package.json, package-lock.json

Commit them separately?
```

## Skips

Skip auto-commit if:
- Broken/unverified code
- Mid-task WIP
- Secret involved (flag/stop instead)

User says "hold off commits": pause this skill for session.
