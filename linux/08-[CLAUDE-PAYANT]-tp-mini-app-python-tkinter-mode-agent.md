# 08 — TP mini-app Python/Tkinter sur **Linux** : Claude Code (PAYANT, mode agent)

> **Public** : étudiants débutants en Python qui veulent voir un vrai agent IA.
> **Coût** : **PAYANT** — abonnement Claude Pro (≈ 20 $/mois) ou tarification API au token.
> **OS** : Linux.
> **Durée** : 30 à 45 min (l'agent fait le boulot).
> **Pré-requis** :
> - Compte Anthropic avec abonnement OU clé API (voir [console.anthropic.com](https://console.anthropic.com))
> - Node.js 18+
> - Python 3.9+ avec Tkinter

> Pour macOS → [`../macos/08-[CLAUDE-PAYANT]-...`](../macos/08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md).
> Pour Windows → [`../windows/08-[CLAUDE-PAYANT]-...`](../windows/08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md).

> **Différence majeure avec OpenClaude (tutos 06/07)** :
> - Avec Claude Code (officiel, payant), le **mode agent** marche vraiment : Claude lit, modifie, crée des fichiers, lance Python, lit l'erreur, corrige tout seul.
> - Tu décris ce que tu veux → Claude le fait. Pas de copier-coller.

---

## Sommaire

1. [Pourquoi cette version payante ?](#1-pourquoi-cette-version-payante-)
2. [Coûts indicatifs](#2-coûts-indicatifs)
3. [Installer Claude Code CLI](#3-installer-claude-code-cli)
4. [Authentification](#4-authentification)
5. [Créer le dossier projet et l'initialiser](#5-créer-le-dossier-projet-et-linitialiser)
6. [Le fichier `CLAUDE.md` (mémoire du projet)](#6-le-fichier-claudemd-mémoire-du-projet)
7. [Étape A — Squelette Tkinter (laisse Claude faire)](#7-étape-a--squelette-tkinter-laisse-claude-faire)
8. [Étape B à E — Construire la mini-app](#8-étape-b-à-e--construire-la-mini-app)
9. [Slash commands utiles](#9-slash-commands-utiles)
10. [Mode plan, diff et permissions](#10-mode-plan-diff-et-permissions)
11. [Bonus — Premier commit Git](#11-bonus--premier-commit-git)
12. [Troubleshooting](#troubleshooting)
13. [Suite](#suite)

---

## 1. Pourquoi cette version payante ?

| Critère | OpenClaude (gratuit) | Claude Code (payant) |
|---------|----------------------|----------------------|
| Mode agent (édite tes fichiers) | aléatoire | **fiable** |
| Compréhension du projet | limitée au contexte | navigue le repo, lit `CLAUDE.md` |
| Slash commands (`/init`, `/plan`, `/diff`) | basique | complet |
| Skills (procédures réutilisables) | basique | mature |
| Coût | 0 € | ~20 $/mois |
| Confidentialité | totale (local) | envoyé à Anthropic |

> **Conclusion pédagogique** : faire les TPs 06 et 07 d'abord pour comprendre **comment** un LLM aide à coder. Puis ce TP 08 pour expérimenter ce que ça donne en mode agent autonome.

[↑ Sommaire](#sommaire)

---

## 2. Coûts indicatifs

Deux modèles de tarification :

### A. Abonnement Claude Pro / Max (recommandé pour étudiants)
- **Pro** : 20 $/mois → quota généreux pour usage individuel
- **Max** : 100-200 $/mois → quota élevé pour usage intensif

### B. API à la consommation (clé `sk-ant-...`)
- Claude Sonnet 4 : ~3 $/M tokens input, ~15 $/M tokens output
- Claude Haiku : ~0,25 $/M input, ~1,25 $/M output
- Pour ce TP entier : ~0,30 à 1 $ avec Sonnet selon ton verbosity

> **Astuce économie** : commence avec Haiku pour les prompts simples, passe à Sonnet pour les refacto complexes.

[↑ Sommaire](#sommaire)

---

## 3. Installer Claude Code CLI

### Vérifier Node.js
```bash
node --version   # ≥ 18
```

Sinon → [tuto 02 §2](./02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md#2-installer-nodejs).

### Installer Claude Code
```bash
npm install -g @anthropic-ai/claude-code
```

Si erreur EACCES :
```bash
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
npm install -g @anthropic-ai/claude-code
```

### Vérifier
```bash
claude --version
claude --help
```

[↑ Sommaire](#sommaire)

---

## 4. Authentification

Au premier lancement de `claude`, le CLI te propose :

### Option A — Connexion à un compte Pro/Max (browser)
1. `claude` te donne une URL.
2. Ouvre-la dans ton navigateur, connecte-toi à ton compte Anthropic.
3. Reviens dans le terminal — c'est connecté.

### Option B — Clé API
```bash
export ANTHROPIC_API_KEY="sk-ant-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
```

Persistant via `~/.bashrc` ou `~/.zshrc`.

> ⚠️ Comme pour OpenRouter, **ne commit jamais** `ANTHROPIC_API_KEY` dans Git.

[↑ Sommaire](#sommaire)

---

## 5. Créer le dossier projet et l'initialiser

```bash
mkdir -p ~/projets/gestionnaire-taches-claude
cd ~/projets/gestionnaire-taches-claude
git init -b main
claude
```

Une fois dans la session interactive `claude`, tape :
```
/init
```

`/init` fait deux choses :
1. Analyse le dossier
2. Crée un `CLAUDE.md` (mémoire du projet)

[↑ Sommaire](#sommaire)

---

## 6. Le fichier `CLAUDE.md` (mémoire du projet)

`CLAUDE.md` est lu par Claude à chaque session. Tu peux y mettre :
- les conventions de code
- la structure du projet
- les commandes pour lancer et tester

Édite `CLAUDE.md` :

~~~markdown
# Gestionnaire de tâches — Tkinter

## Stack
- Python 3.10+
- Tkinter (stdlib)
- Persistance JSON simple

## Conventions
- Classe `TaskApp` pour l'UI
- Classe `TaskRepository` pour la persistance JSON
- Pas de commentaires qui paraphrasent le code
- f-strings, type hints partout
- Conformité PEP 8

## Lancer
```bash
python3 main.py
```

## Tests manuels
1. Ajouter "tâche A" → apparaît dans la liste
2. Cliquer "Faite" → préfixe "[X] "
3. Re-cliquer "Faite" → toggle off
4. Bouton "Supprimer" → enlève la sélection
5. Fermer/relancer → tâches persistantes (tasks.json)
~~~

Sauvegarde. Maintenant Claude connaît ton projet.

[↑ Sommaire](#sommaire)

---

## 7. Étape A — Squelette Tkinter (laisse Claude faire)

Dans la session `claude`, tape :

```
Crée main.py : une fenêtre Tkinter 400x500 intitulée "Mon Gestionnaire de Tâches"
avec un titre "Mes tâches" centré en haut. Utilise la classe TaskApp comme indiqué
dans CLAUDE.md.
```

Claude va :
1. Te montrer ce qu'il va faire
2. Te demander confirmation pour créer le fichier
3. Créer `main.py`

Vérifie :
```bash
ls
cat main.py
python3 main.py
```

Une fenêtre doit s'ouvrir.

[↑ Sommaire](#sommaire)

---

## 8. Étape B à E — Construire la mini-app

### Étape B — Ajouter une tâche
```
Ajoute à TaskApp un Entry, un bouton "Ajouter" et une Listbox.
La liste self.tasks (list[str]) garde l'état. Vide l'Entry après ajout.
```

### Étape C — Supprimer
```
Bouton "Supprimer" qui retire la tâche sélectionnée. Si rien n'est sélectionné, ne fais rien.
```

### Étape D — Toggle "Faite"
```
Bouton "Faite" qui préfixe la tâche par "[X] " (toggle si déjà préfixée).
```

### Étape E — Persistance JSON
```
Crée TaskRepository (lecture/écriture tasks.json) selon CLAUDE.md.
TaskApp utilise TaskRepository à chaque modif et au démarrage.
Gère JSONDecodeError (démarre vide).
Mets à jour CLAUDE.md pour refléter la nouvelle structure.
```

> Claude va modifier plusieurs fichiers d'un coup. Après chaque étape, lance `python3 main.py` pour tester.

[↑ Sommaire](#sommaire)

---

## 9. Slash commands utiles

| Commande | Effet |
|----------|-------|
| `/init` | Initialise CLAUDE.md |
| `/help` | Aide |
| `/clear` | Reset la conversation (garde CLAUDE.md) |
| `/compact` | Résume l'historique (économise les tokens) |
| `/plan` | Active le mode plan (Claude propose avant d'agir) |
| `/diff` | Montre les modifs faites depuis le début |
| `/context` | Voir ce qui est dans le contexte |
| `/permissions` | Gérer les permissions (auto-edit, auto-bash, etc.) |
| `/status` | État de la session (modèle, tokens utilisés…) |
| `/model` | Changer de modèle (Sonnet ↔ Haiku) |

[↑ Sommaire](#sommaire)

---

## 10. Mode plan, diff et permissions

### Mode plan
```
/plan
```

Active un mode où Claude propose **d'abord** un plan textuel, attend ton OK, puis exécute. Idéal pour les refactos importants.

### Voir les diffs
```
/diff
```

Montre toutes les modifications de fichiers de la session.

### Permissions
```
/permissions
```

Tu peux régler :
- Auto-write (création de fichiers sans demander)
- Auto-edit (modification de fichiers sans demander)
- Auto-bash (exécution de commandes sans demander)

Pour un débutant : **garde tout sur "ask"**. Pour un dev pro : auto-edit OK, auto-bash dangereux.

[↑ Sommaire](#sommaire)

---

## 11. Bonus — Premier commit Git

Tu peux même demander à Claude :

```
Fais un commit Git avec un message descriptif des changements.
```

Claude va `git add`, `git commit -m "..."`, et te montrer le résultat.

Ou manuellement :
```bash
cat > .gitignore <<'EOF'
__pycache__/
*.pyc
.venv/
.env
EOF

git add .
git commit -m "TP mini-app : gestionnaire de tâches Tkinter avec Claude Code"
git log --oneline
```

[↑ Sommaire](#sommaire)

---

## Troubleshooting

| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| `claude: command not found` | npm pas dans le PATH | `source ~/.bashrc` ou ferme/rouvre le terminal |
| `401 unauthorized` | Pas connecté | Relance `claude` et fais l'auth, ou `export ANTHROPIC_API_KEY=...` |
| `429 rate limited` | Trop de requêtes | Attends 1 min ; passe sur Haiku via `/model` |
| `insufficient_quota` | Crédits/abonnement épuisé | Recharge sur [console.anthropic.com/billing](https://console.anthropic.com/billing) |
| Claude refuse une commande shell | Permissions strictes | `/permissions` → autorise la commande spécifique |
| `CLAUDE.md` ignoré | Pas dans le bon dossier | Lance `claude` depuis le dossier qui contient `CLAUDE.md` |
| Fichiers modifiés sans accord | Auto-edit activé | `/permissions` → repasse `edit` sur "ask" |
| Ne trouve pas un fichier | Hors du workspace | Tu dois lancer `claude` depuis la racine du projet |
| Trop de tokens consommés | Historique trop long | `/compact` ou `/clear` |

[↑ Sommaire](#sommaire)

---

## Suite

- [`06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md`](./06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md) — comparer avec la version locale gratuite (mode chat)
- [`07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md`](./07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md) — comparer avec la version cloud gratuite
- [Documentation officielle Claude Code](https://docs.claude.com/en/docs/claude-code/overview)

[↑ Sommaire](#sommaire)
