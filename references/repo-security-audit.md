# Security Audit Checklist — Third-Party Repos

Checklist à appliquer **systématiquement** avant d'intégrer du code d'un repo listé dans ce skill (ou tout repo externe).

## Phase 1 — Périmètre

1. **Identifier ce qu'on va exécuter/inclure**
   - Scripts Python ? Librairie pip ? Binaire ? Skill Claude Code ?
2. **Déterminer les permissions nécessaires**
   - Réseau ? Système de fichiers ? Variables d'env ?

## Phase 2 — Audit du code source

### 🔑 Credentials hardcodés

```bash
grep -nE "(api_key|API_KEY|TOKEN|secret|password|passwd|auth_token)" *.py *.json *.sh 2>/dev/null
```

- ✅ Vérifier que les clés ne sont que des placeholders (***, YOUR_KEY_HERE)
- ✅ Vérifier que `.gitignore` exclut les vrais fichiers de credentials

### 📡 Télémesure / exfiltration

```bash
grep -nE "(telemetry|tracking|analytics|beacon|exfiltrat|heartbeat|datadog|sentry|segment|amplitude)" *.py 2>/dev/null
```

- ✅ Aucun appel vers des serveurs inconnus
- ✅ Aucun envoi de données système (hostname, username, path)

### 🌐 Requêtes réseau non documentées

```bash
grep -rnE "(requests\.(get|post)|httpx\.(get|post)|urllib|aiohttp\.ClientSession|curl|wget)" . 2>/dev/null
```

- ✅ Chaque URL doit correspondre à un service documenté (yfinance, Yahoo, Finnhub, etc.)
- ✅ Les URLs optionnels (APIs payantes) doivent être gardés par variable d'environnement

### 🕵️ Code dynamique dangereux

```bash
grep -nE "(eval|exec|compile|__import__|os\.system|subprocess\.(call|Popen|run)|pickle\.loads)" *.py 2>/dev/null
```

- ✅ Aucun `eval()` / `exec()` / `pickle.loads()` non justifié
- ✅ Les `subprocess` doivent être dans un périmètre explicite

### 📦 Dépendances

- ✅ Examiner `requirements.txt` / `pyproject.toml`
- ✅ Vérifier que ce sont des libs connues (pypi.org), pas des packages obscurs

## Phase 3 — CI / Build

```bash
# Vérifier les workflows GitHub pour des actions non autorisées
curl -sL "https://raw.githubusercontent.com/$ORG/$REPO/$BRANCH/.github/workflows/*.yml"
```

- ✅ Pas de `curl | bash` ou pipe vers shell dans les workflows
- ✅ Pas de téléchargement de binaires depuis des URLs non vérifiées

## Phase 4 — Fichiers de configuration

- ✅ `.env.example` / `config/*.example.json` : placeholders uniquement
- ✅ `.gitignore` : les bons fichiers sont exclus (`.env`, `*.key`, `credentials`)
- ✅ `Dockerfile` : pas de port exposé inutilement, pas de secrets

## Phase 5 — Décision

| Résultat | Action |
|----------|--------|
| ✅ Tous les checks passent | Utilisation possible |
| ⚠️ Anomalie mineure (lib optionnelle non auditée) | Intégrer + surveiller |
| ❌ Credentials hardcodés ou télémesure | **Ne pas utiliser**, signaler |
