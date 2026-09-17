# Security Audit Checklist — Third-Party Code Integration

Utilisez cette checklist avant d'intégrer du code provenant d'un dépôt GitHub externe dans un projet.

## Phase 1 : Inspection des dépendances

- [ ] Lire `requirements.txt` / `package.json` — chaque lib est-elle légitime et connue ?
- [ ] Vérifier les dépendances optionnelles (`extras_require`, `optionalDependencies`) — pourraient cacher du code malveillant
- [ ] Checker les scripts `setup.py` / `preinstall` / `postinstall` pour des commandes suspectes

## Phase 2 : Scan du code

- [ ] `grep -rE "(requests|httpx|urllib|axios|fetch)" scripts/` — vers où vont les requêtes réseau ?
- [ ] `grep -rE "(telemetry|tracking|analytics|beacon|exfiltrat|phone.home)"` — y a-t-il du phoning home ?
- [ ] `grep -rE "(eval|exec|Function\(|setTimeout\(.*code|child_process)"` — exécution de code dynamique ?
- [ ] `grep -rE "(secret|password|token|api_key|apikey)"` — credentials hardcodés ou places sensibles ?
- [ ] `grep -rE "(base64|atob|btoa|escape|unescape|decodeURI)"` — obfuscation potentielle ?
- [ ] `grep -rnE "curl.*\|.*(bash|sh)"` — pipe-to-shell dangereux dans la doc ?

## Phase 3 : Config et credentials

- [ ] Vérifier `.gitignore` — les fichiers d'API keys y sont-ils ?
- [ ] Lire les fichiers d'exemple (`*.example.*`) — contiennent-ils uniquement des placeholders ?
- [ ] Vérifier les `.env` / `.env.example` / `.env.local`
- [ ] Checker les `.mcp.json` — serveurs activés par défaut ou pas ?
- [ ] Variables d'environnement documentées dans README ?

## Phase 4 : Réseau et exfiltration

- [ ] Lire chaque script fetch/API — les endpoints sont-ils documentés et légitimes ?
- [ ] Y a-t-il des appels vers des IPs hardcodées ou des domaines inconnus ?
- [ ] Les APIs optionnelles sont-elles bien derrière des variables d'environnement (pas hardcodées) ?
- [ ] Y a-t-il du WebSocket non documenté ?

## Phase 5 : Exécution

- [ ] Y a-t-il des `eval()` / `exec()` / `compile()` sur des entrées utilisateur ?
- [ ] Y a-t-il des `subprocess` / `child_process` avec des arguments non sanitizés ?
- [ ] Les scripts acceptent-ils des flags `--json` ou `--output` qui pourraient être détournés ?
- [ ] Les chemins de fichiers sont-ils sanitizés (path traversal) ?

## Phase 6 : Dépendances aval (post-intégration)

- [ ] Les nouvelles dépendances introduisent-elles des conflits de versions ?
- [ ] Les droits d'accès aux fichiers/crédentials sont-ils minimaux ?
- [ ] Les tokens API sont-ils injectés via variables d'environnement (pas hardcodés) ?
- [ ] Le code intégré tourne-t-il bien dans le sandbox (Docker) ?

## Exemple : Audit webcoda-sydney/trading-agents (propre)

```
grep result: aucun hardcoded credential, aucun appel télémesure,
aucun eval/exec, pas de pipe-to-shell, .gitignore bien configuré,
MCP servers disabled par défaut, dépendances légitimes.
→ ✅ RAS, intégration sûre.
```

## Signaux d'alarme (STOP — ne pas intégrer sans investigation)

- 🔴 Dernier commit > 2 ans (projet abandonné)
- 🔴 Fichier `.env` commité (credentials exposés)
- 🔴 `curl http://... | bash` dans la doc
- 🔴 Appels vers des domaines inconnus sans documentation
- 🔴 Base64 / chiffrement sans raison évidente
- 🔴 Licence absente ou incompatible
- 🔴 Étoiles suspectes (pic soudain — farming)
- 🔴 Review du code trop complexe pour ce que le projet prétend faire
