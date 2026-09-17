# Hermes Profile Skills Mapping

Architecture de référence pour configurer des profils Hermes spécialisés pour le Kanban. Chaque profil combine des skills Hermes natifs (chargés directement) et des skills Claude Code (délégation via `terminal("claude -p ...")`).

## Principe

Les skills Hermes sont globaux — tous les profils y ont accès. Ce qui différencie un profil, c'est son **SOUL.md** qui lui dit quels skills utiliser pour quelle tâche.

Le SOUL.md de chaque profil contient deux sections :
1. **Skills Hermes natifs** — table `Tâche → Skills à charger`, piochant dans les 97 skills installés
2. **Délégation Claude Code** — commande `terminal()` + liste de repos GitHub recommandés

## Architecture à 7 profils (testée sur un setup CTO/investisseur)

| Profil | Modèle | Clone de | Rôle | Spécialisation |
|--------|--------|----------|------|---------------|
| `dev` | deepseek-v4-pro | deepseek-pro | Full-stack + blockchain | baldcoin, bookpass.fr, trading-bots |
| `trading` | deepseek-v4-flash | default | Trading bots (paper) | 8 daily + 3 intraday Kraken WS |
| `devops` | deepseek-v4-pro | deepseek-pro | Infra | Docker, Traefik, Coolify |
| `research` | deepseek-v4-flash | default | Veille + synthèse | CFA, Les Echos, marchés |
| `reviewer` | deepseek-v4-pro | deepseek-pro | Code review | PRs, sécurité, qualité |
| `design` | deepseek-v4-pro | deepseek-pro | UI/UX + identité | bookpass, baldcoin |
| `marketing` | deepseek-v4-pro | deepseek-pro | Stratégie + growth | SEO, social media, campagnes |

### Règle de choix du modèle
- **deepseek-v4-flash** : tâches légères, quotidiennes, à faible coût (trading, veille, synthèse)
- **deepseek-v4-pro** : tâches critiques qualité (code production, déploiements, review, design, stratégie)

## Skills Hermes natifs → Profils (mapping complet)

### `dev`
| Tâche | Skills |
|-------|--------|
| Planification | `plan`, `spike`, `writing-plans` |
| Développement | `test-driven-development`, `systematic-debugging`, `subagent-driven-development` |
| Code Review | `requesting-code-review`, `simplify-code`, `github-code-review` |
| Git/GitHub | `github-pr-workflow`, `github-issues`, `github-repo-management` |
| Blockchain | `solana-dev` |
| Inspection | `codebase-inspection` |

### `trading`
| Tâche | Skills |
|-------|--------|
| Analyse trading | `cfa-skills`, `paper-trading-bots` |
| Données marché | `polymarket`, `authenticated-web-scraping` |
| Veille crypto | `blogwatcher`, `youtube-content` |
| Notation | `obsidian` |

### `devops`
| Tâche | Skills |
|-------|--------|
| Déploiement Coolify | `coolify-service-management` |
| Diagnostic serveur | `server-ram-diagnostics` |
| Automatisation | `webhook-subscriptions`, `authenticated-web-scraping` |
| Debugging | `systematic-debugging` |

### `research`
| Tâche | Skills |
|-------|--------|
| Recherche académique | `arxiv`, `llm-wiki` |
| Veille contenu | `blogwatcher`, `youtube-content` |
| Données marché | `polymarket` |
| Prise de notes | `obsidian` |
| Synthèse | `humanizer` |

### `reviewer`
| Tâche | Skills |
|-------|--------|
| Revue de code | `github-code-review`, `requesting-code-review` |
| Nettoyage | `simplify-code` |
| Debugging | `systematic-debugging` |
| Inspection | `codebase-inspection` |

### `design`
| Tâche | Skills |
|-------|--------|
| Maquettes rapides | `sketch`, `claude-design` |
| Design système | `popular-web-designs` |
| Prototypes | `excalidraw`, `architecture-diagram` |
| Art génératif | `p5js`, `pixel-art`, `ascii-art` |
| Infographies | `baoyu-infographic` |
| Animations | `bookpass-motion` |
| Reverse-engineering | `taste` (hub-installed) |

### `marketing`
| Tâche | Skills |
|-------|--------|
| SEO | `seo` (hub-installed) |
| Rédaction | `humanizer` |
| Design marketing | `baoyu-infographic`, `baoyu-comic` |
| Recherche | `blogwatcher`, `youtube-content`, `polymarket` |
| Social media | `xurl` |
| Assets visuels | `gif-search`, `pixel-art` |

## Skills Claude Code → Profils (délégation)

Chaque profil délègue les tâches lourdes à Claude Code via `terminal(command="cd /workspace && claude -p '...' --allowedTools Read,Edit,Bash --max-turns N")`.

| Profil | Skills Claude Code recommandés | max-turns |
|--------|------------------------------|-----------|
| `dev` | `anthropics/skills`, `addyosmani/agent-skills`, `claude-code-best-practice`, `tech-debt-skill` | 50 |
| `trading` | `trading-agents`, `JoelLewis/finance_skills`, `pycoingecko` | 20 |
| `devops` | `addyosmani/agent-skills`, `repomix` | 30 |
| `research` | `Agent-Reach`, `crawl4ai`, `caveman` | 20 |
| `reviewer` | `code-review-graph`, `Anthropic-Cybersecurity-Skills`, `skill-security-scan` | 30 |
| `design` | `impeccable`, `taste-skill`, `website-builder-setup` + 21st.dev | 30 |
| `marketing` | `claude-seo`, `marketingskills`, `aaron-marketing-skills`, `social-ai-team`, `x-algo-skill` | 25 |

## Installation de skills depuis le hub Hermes

```bash
# Installation interactive (demande confirmation)
hermes skills install skills-sh/senlindesign/taste-skill/taste

# Installation non-interactive
hermes skills install --yes skills-sh/senlindesign/taste-skill/taste

# Forcer malgré le blocage du scanner de sécurité
hermes skills install --yes --force skills-sh/agricidaniel/claude-seo/seo
```

### Pièges
- Le scanner de sécurité bloque les skills avec verdict CAUTION (exfiltration, etc.)
- Les skills community nécessitent `--force` pour passer le blocage
- Toujours vérifier la source avant de forcer l'installation
- Les skills hub sont installés globalement, pas par profil

## Création de profil avec `hermes profile`

```bash
# Créer en clonant un profil existant (hérite config + skills + .env)
hermes profile create trading --clone-from default

# Résultat : wrapper CLI dans /root/.local/bin/trading
# Config : /root/.hermes/profiles/trading/
# SOUL.md : /root/.hermes/profiles/trading/SOUL.md
```

## Checklist de création d'un nouveau profil

1. `hermes profile create <nom> --clone-from <profil-source>`
2. Choisir le bon modèle source (flash pour léger, pro pour qualité)
3. Éditer `/root/.hermes/profiles/<nom>/SOUL.md` avec :
   - Règles métier spécifiques
   - Table `Skills Hermes natifs`
   - Section `Délégation Claude Code`
4. Installer les skills hub manquants si nécessaire
5. Vérifier : `hermes profile list`
