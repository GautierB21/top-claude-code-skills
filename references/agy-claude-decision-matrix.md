# Agy vs Claude Code — Decision Matrix

## Tools

| | Antigravity (`agy`) | Claude Code (`claude`) |
|---|---|---|
| **Version** | 1.1.2 | 2.1.210 |
| **Models** | Gemini 3.5 Flash (3 tiers), Gemini 3.1 Pro (2 tiers), Claude Sonnet 4.6 Thinking, Claude Opus 4.6 Thinking, GPT-OSS 120B | Claude models (via Anthropic/Bedrock/Vertex API) |
| **Strengths** | Multi-model, Gemini Flash = ultra-fast/cheap, sandbox mode, `accept-edits`/`plan` modes | Skills ecosystem (150k+ repos), MCP, production-grade code quality |
| **Skills** | No | Yes (CLAUDE.md, repos GitHub) |
| **MCP** | Yes | Yes |
| **Sub-agents** | Yes (native parallel) | Yes (`--agents` JSON) |
| **Sandbox** | Yes (`--sandbox`) | Yes |
| **Install** | `/root/.local/bin/agy` | `/root/.local/bin/claude` |

## 3-Tier Decision Model

```
⚡ FLASH (agy + Gemini 3.5 Flash)
  Ultra-fast, near-zero cost
  → scaffolding, boilerplate, scraping, brainstorming, healthchecks, creative variations
  Model: "Gemini 3.5 Flash (Low|Medium|High)"

🔶 PRO (agy + Gemini 3.1 Pro)
  High quality, still faster than Claude
  → code intermédiaire, refacto simple, new modules, prototypes, multi-source analysis
  Model: "Gemini 3.1 Pro (Low|High)"

🟣 CLAUDE (claude CLI + Claude API)
  Maximum quality, full skills ecosystem
  → production code, security, complex refacto, code review, final deliverables
```

## Per-Task-Type Mapping

| Task type | ⚡ Flash | 🔶 Pro | 🟣 Claude |
|-----------|:-------:|:------:|:--------:|
| Scaffolding / boilerplate | ✅ | Overkill | Overkill |
| Simple scripts (< 50 lines) | ✅ | ✅ | Overkill |
| Prototype / POC | ✅ | ✅ | ❌ (too slow) |
| Creative exploration (5+ variants) | ✅ Flash Low | — | — |
| Web scraping (simple) | ✅ Flash Med | — | — |
| Code 50-200 lines (logic heavy) | ❌ | ✅ | ⚠️ if critical |
| New feature module | ❌ | ✅ | ✅ |
| Config files (Docker, Nginx) | ❌ | ⚠️ | ✅ |
| Production refacto | ❌ | ❌ | ✅ |
| Security audit | ❌ | ❌ | ✅ |
| Code review | ❌ | ❌ | ✅ |
| Final copywriting / content | ❌ | ❌ | ✅ |
| Plan marketing complet | ❌ | ❌ | ✅ |

## The "agy explores → Claude executes" Pattern

1. `agy -p --model "Gemini 3.5 Flash (Low)" "Generate 5 approaches for X"` → 15s each
2. Human picks the best approach
3. `claude -p "Implement approach #3 with production quality" --allowedTools Read,Edit,Bash --max-turns 50`

## Anti-Patterns

- **Never use agy for security-critical work** (review, audit, secrets handling)
- **Never create a "proxy" Hermes profile** that just delegates to agy/claude — the Hermes profile must own the Kanban lifecycle
- **Don't use Claude Code for throwaway work** — Flash is 3-5x faster and nearly free
- **Don't use agy for tasks needing the skills ecosystem** — Claude Code's CLAUDE.md + repos are irreplaceable

## Profile SOUL.md Template

```markdown
## Délégation : Agy (3 niveaux) vs Claude Code

| Niveau | Type de tâche | Outil | Modèle | Commande |
|--------|--------------|-------|--------|----------|
| ⚡ Flash | <task examples> | `agy` | Gemini 3.5 Flash (<tier>) | `terminal(command="agy -p '<prompt>' --model 'Gemini 3.5 Flash (<tier>)'")` |
| 🔶 Pro | <task examples> | `agy` | Gemini 3.1 Pro (High) | `terminal(command="agy -p '<prompt>' --model 'Gemini 3.1 Pro (High)'")` |
| 🟣 Claude | <task examples> | `claude` | Claude | `terminal(command="cd /workspace && claude -p '<prompt>' --allowedTools Read,Edit,Bash --max-turns N")` |
```
