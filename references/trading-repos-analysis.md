# Trading Repos Analysis — Deep Dive

## HKUDS/Vibe-Trading (17k⭐)

**Lien :** https://github.com/HKUDS/Vibe-Trading
**Stack :** Python (FastAPI) + React 19 (ECharts) | `pip install vibe-trading-ai`
**Actif :** 425+ commits, commit quotidien, 107 branches, 42 tags
**Source :** University of Hong Kong Data Science Lab

### Architecture
```
agent/                    — LLM agent loop, providers, skills, memory
frontend/                 — React 19 SPA (Vite)
scripts/                  — Utilitaires / dev
tools/                    — Outils backtest, alpha zoo
wiki/                     — Wiki public sur vibetrading.wiki
.claude/                  — Claude Code skills
```

### Features clés
| Module | Détail |
|--------|--------|
| 🤖 **Self-Improving Agent** | Mémoire persistante, skills éditables, FTS5 session search, 5-layer context compression |
| 🐝 **Multi-Agent Swarm** | Investment/quant/crypto/risk teams, streaming progress, persisted reports |
| 📊 **Cross-Market Backtest** | A/HK/US equities, crypto, futures, forex — 18+ data sources avec auto-fallback |
| 👥 **Shadow Account** | Upload journal broker → extraction règle → backtest shadow → rapport 8 sections |
| 🧬 **Alpha Zoo** | 456 pre-built quant alphas (Qlib 158 + Kakushadze 101 + GTJA 191 + academic) |
| 📱 **IM Channels** | 16 adapters : Telegram, Slack, Discord, Matrix, WhatsApp, Signal, WeChat, Email… |
| 🔌 **Brokers** | Robinhood (agentic trading), IBKR, Alpaca, Trading 212, Zerodha… |
| 🔬 **Research Autopilot** | Hypothèse → signal engine → backtest → rapport, 68 tools |

### Sécurité (auditée rapidement)
- Mises à jour récentes sécurité : CSRF hardening, API auth key, sandbox file tools, shell tools opt-in
- Docker non-root user, CORS restrictif en remote
- Actif : correctif de sécurité tous les 2-3 jours

### Verdict
**Workspace de recherche IA**, pas un bot automatisé. Chaque décision = appel LLM (coûteux). Complémentaire à notre projet : utiliser pour explorer des stratégies, pas pour les exécuter.

---

## webcoda-sydney/trading-agents (3⭐)

Branch: `feature/dev`. 7 months old. 5 commits by PeterWebb-webcoda + Claude.
**⚠️ Audit sécurité réalisé le 2026-07-03 : ✅ RAS**

### Résultat d'audit sécurité
| Contrôle | Résultat |
|----------|----------|
| Hardcoded credentials | ✅ Aucun — api-keys.json dans .gitignore, placeholder values |
| Télémesure / tracking | ✅ Aucun — pas d'appel vers des serveurs inconnus |
| Réseau | ✅ Uniquement yfinance + APIs optionnelles via env vars |
| Obfuscation / backdoor | ✅ Code clair, pas d'eval/exec |
| CLAUDE.md | ✅ Aucune commande pipe dangereuse |
| .mcp.json | ✅ Tous les serveurs désactivés par défaut (disabled: true) |
| Dépendances | ✅ yfinance, pandas, numpy, pandas_ta — libs légitimes |

### Structure
```
.claude/
  skills/
    stock-data.md          — Documentation des 5 scripts
    portfolio-management.md
    api-providers.md
  agents/                  — 24 agents
  commands/
config/
  api-keys.example.json    — Placeholder UNIQUEMENT
  trading-rules.json
data/
scripts/
  fetch_price.py           — Prix + métriques (P/E, market cap, performance 1sem/1mois/YTD)
  fetch_fundamentals.py    — Bilan complet (ROE, ROA, ROIC, D/E, marges)
  fetch_technicals.py      — pandas_ta : RSI, MACD, Bollinger, ATR, Stochastics, trends
  fetch_news.py            — News + scoring sentiment (keyword-based + Finnhub/Alpha Vantage optionnel)
  fetch_portfolio.py       — Valorisation portfolio
  requirements.txt         — yfinance, pandas, numpy, pandas_ta
.mcp.json                  — MCP servers (tous disabled:true)
CLAUDE.md                  — 24 agents workflow
```

### 24 Agents — 8 Teams
1. **Analysis** (5): fundamental, technical, sentiment, news, macro
2. **Research** (3): bullish, bearish, equity-synthesis
3. **Execution** (3): risk-manager (MUST approve trades), portfolio-manager, trade-executor
4. **Strategy** (5): value, growth, momentum, dividend, options
5. **Market** (2): crypto, forex
6. **Regional** (2): asx, us-market
7. **Coaching** (2): trading-coach, trade-reviewer
8. **Discovery** (2): market-analyst, stock-picker

### Data Sources
| Source | API Key | Coverage |
|--------|---------|----------|
| yfinance | Non | Global stocks, crypto, forex |
| Alpha Vantage | Oui (free tier) | US stocks, 60+ indicators |
| Finnhub | Oui (free tier) | Global, real-time, sentiment |
| Twelve Data | Oui (free tier) | Global, 100+ indicators |

### Intégration dans notre projet (réalisée)
- Scripts data copiés dans `/root/trading-bots/scripts/`
- `pandas-ta` ajouté aux dépendances
- Stratégie **Fundamental** (P/E, ROE, D/E) créée → trades JPM
- Stratégie **Sentiment** (news scoring) créée → trades NVDA
- Les scripts ne sont PAS importés par les stratégies (yfinance direct, isolation propre)

---

## TraderAlice/OpenAlice (5.8k⭐)

Branch: `master`. Very active (commits hours old). 1,490+ commits, 107 branches, 42 tags.

### Features
- Equities, crypto, commodities, forex, macro
- Real brokerage (IBKR)
- Self-describing market data vendors
- Credential injection smoke testing
- Desktop app (packaged binary)

### Verdict
Full production trading agent — desktop app, real-money brokerage. Architecture de référence mais pas intégrable comme lib.

---

## Fincept-Corporation/FinceptTerminal (27.8k⭐)

Branch: `main`. 1,034 commits. **⚠️ En maintenance réduite depuis juin 2026** (1 update/mois, focus sur version payante + nouveau projet Quantcept en Node.js).

### Stack
**C++20 + Qt6** — application native lourde. Pas une lib Python.

### Features
- Multi-asset analytics (DCF, portfolio optimization, VaR, Sharpe)
- 37 AI agents (Buffett, Graham, Lynch, Klarman…)
- 100+ data connectors (Polygon, FRED, FMI, Banque Mondiale…)
- 16 broker integrations
- QuantLib : 18 modules quantitatifs
- AI Quant Lab : ML, factor discovery, HFT, reinforcement learning

### Verdict
Bloomberg-like desktop app open-source. Trop lourd pour notre besoin. Le nouveau projet Quantcept (Node.js) est plus dans notre philosophie mais pas encore mature.

---

## JoelLewis/finance_skills (145⭐)

### Structure
- 81 skills Claude Code couvrant 7 domaines
- Workspace config + evals
- Plugin Claude Code (skills definitions, pas du code exécutable)

### 7 Domaines
1. Investment management
2. Compliance
3. Advisory practice
4. Trading
5. Operations
6. (2 non nommés)

### Verdict
Skill pack pour Claude Code — à charger quand on bosse sur le projet pour les bonnes pratiques financières, pas de code à intégrer.

---

## Lessons Learned for Bot Building

1. **Toujours auditer la sécurité avant intégration** : vérifier credentials hardcodés, télémesure, eval/exec, dépendances
2. **LLM ≠ Trading** : les décisions des bots doivent être déterministes (règles en dur). Le LLM fait le reporting et l'analyse, pas les trades
3. **Découpage frontend/API** : Flask + SPA HTML marche très bien pour un dashboard de monitoring
4. **Docker + Coolify** : le pattern container_name + labels Traefik + réseau coolify fonctionne pour bookpass.fr
