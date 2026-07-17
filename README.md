# mycc

Domain-organized library of copy-paste-ready AI agent skills — for Claude Code, Antigravity, Cursor, or any tool that supports project-local agent/skill configuration.

## Folder structure

```
skills/
  design/       # visual/UI/design-system skills
  dev/          # engineering workflow skills (e.g. commit-skill)
  documents/    # doc generation, formatting, reports
  research/     # investigation, search, synthesis
  writing/      # prose, copy, editing
agents/         # standalone subagent definitions (e.g. git-committer)
.claude/        # Claude Code project config — mirrors skills/agents you've adopted
```

Each domain under `skills/` groups related capabilities. A skill is a self-contained folder with a `SKILL.md` — sometimes paired with an `agent` it forks into (see `agents/`).

## Using a skill

1. Browse `skills/<domain>/` for the one you need.
2. Copy the skill folder into your tool's project config — for Claude Code, that's `.claude/skills/<skill-name>/`. If it references an agent (check its `SKILL.md` frontmatter for `agent:`), copy the matching file from `agents/` into `.claude/agents/` too.
3. Commit `.claude/` to the target repo. The tool picks up the skill automatically.

Example — adopting `commit-skill`:

```
cp -r skills/dev/commit-skill  <target-repo>/.claude/skills/
cp    agents/git-committer.md  <target-repo>/.claude/agents/
```

## Why local, not global

Skills here are meant to be **copied per-repo**, not installed once into a global config:

- **Portable across tools.** A skill folder with a `SKILL.md` works the same whether the target uses Claude Code, Antigravity, or Cursor — no tool-specific install step to maintain.
- **Explicit and auditable.** What's in `.claude/` is exactly what that repo uses — reviewable in a PR, not hidden in a machine-wide config that silently affects every project.
- **No cross-project bleed.** A skill tuned for one repo's conventions (commit style, doc format, etc.) doesn't leak into unrelated projects or override their own local skills.
- **Versioned with the code.** Once copied in, the skill evolves with the repo's git history instead of drifting out of sync with a separately-updated global install.

`mycc` itself is the source library — pull from it, don't point tools at it directly.
