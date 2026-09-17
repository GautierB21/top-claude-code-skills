# Installing GitHub-Based Claude Code Skills

Claude Code loads skills from `.claude/skills/` inside the project directory. Many open-source skills on GitHub follow the same directory convention.

## Generic Installation

```bash
# 1. Clone the repo
cd /tmp
git clone --depth 1 https://github.com/OWNER/REPO.git

# 2. Copy the skills
mkdir -p /path/to/project/.claude/skills
cp -r /tmp/REPO/.claude/skills/* /path/to/project/.claude/skills/

# 3. Copy settings hooks (if present)
cp /tmp/REPO/.claude/settings.json /path/to/project/.claude/settings.json 2>/dev/null || true

# 4. Copy supplementary files (PRODUCT.md, DESIGN.md, skill/ directory) if the skill needs them
cp /tmp/REPO/PRODUCT.md /tmp/REPO/DESIGN.md /path/to/project/ 2>/dev/null || true
cp -r /tmp/REPO/skill /path/to/project/.claude/skill 2>/dev/null || true

# 5. Cleanup
rm -rf /tmp/REPO
```

## Example: Impeccable

```bash
cd /tmp
git clone --depth 1 https://github.com/pbakaus/impeccable.git
mkdir -p /path/to/project/.claude/skills
cp -r /tmp/impeccable/.claude/skills/* /path/to/project/.claude/skills/
cp /tmp/impeccable/.claude/settings.json /path/to/project/.claude/settings.json
cp /tmp/impeccable/DESIGN.md /path/to/project/
cp /tmp/impeccable/PRODUCT.md /path/to/project/
cp -r /tmp/impeccable/skill /path/to/project/.claude/skill
rm -rf /tmp/impeccable
```

## Activation

- Skills in `.claude/skills/` are auto-discovered by Claude Code on next run.
- Hooks in `.claude/settings.json` run automatically after matching tool use.
- Run the skill context script if one exists: `node .claude/skills/<name>/scripts/context.mjs`

## Pitfalls

- Some skills (like Impeccable) ship their own PRODUCT.md and DESIGN.md that describe THEIR product, not yours. When using them as design guardrails: the sub-agent must use YOUR project tokens, not the skill tokens.
- Settings hooks in `.claude/settings.json` overwrite your existing hooks if you copy them blindly. Check the file first.
- Skills installed this way are per-project, not global. Repeat for each project.
