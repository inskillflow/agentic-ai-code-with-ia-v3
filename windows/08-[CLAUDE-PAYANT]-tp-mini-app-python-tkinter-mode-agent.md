# 08 — TP mini-app Python/Tkinter sur **Windows** : Claude Code (PAYANT, mode agent)

> **Public** : étudiants débutants en Python qui veulent voir un vrai agent IA.
> **Coût** : **PAYANT** — abonnement Claude Pro (≈ 20 $/mois) ou tarification API au token.
> **OS** : Windows 10 / 11.
> **Durée** : 30 à 45 min (l'agent fait le boulot).
> **Pré-requis** :
> - Compte Anthropic avec abonnement OU clé API (voir [console.anthropic.com](https://console.anthropic.com))
> - Node.js 18+
> - Python 3.9+ avec Tkinter
> **Terminal** : PowerShell 7 recommandé.

> Pour Linux → [`../linux/08-[CLAUDE-PAYANT]-...`](../linux/08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md).
> Pour macOS → [`../macos/08-[CLAUDE-PAYANT]-...`](../macos/08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md).

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

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Critère | OpenClaude (gratuit) | Claude Code (payant) |
|---------|----------------------|----------------------|
| Mode agent (édite tes fichiers) | aléatoire | **fiable** |
| Compréhension du projet | limitée au contexte | navigue le repo, lit `CLAUDE.md` |
| Slash commands (`/init`, `/plan`, `/diff`) | basique | complet |
| Skills (procédures réutilisables) | basique | mature |
| Coût | 0 € | ~20 $/mois |
| Confidentialité | totale (local) | envoyé à Anthropic |

> **Conclusion pédagogique** : faire les TPs 06 et 07 d'abord pour comprendre **comment** un LLM aide à coder. Puis ce TP 08 pour expérimenter l'agent autonome.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 2. Coûts indicatifs

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Deux modèles de tarification :

### A. Abonnement Claude Pro / Max (recommandé pour étudiants)
- **Pro** : 20 $/mois → quota généreux pour usage individuel
- **Max** : 100-200 $/mois → quota élevé pour usage intensif

### B. API à la consommation (clé `sk-ant-...`)
- Claude Sonnet 4 : ~3 $/M tokens input, ~15 $/M tokens output
- Claude Haiku : ~0,25 $/M input, ~1,25 $/M output
- Pour ce TP entier : ~0,30 à 1 $ avec Sonnet selon ton verbosity

> **Astuce économie** : commence avec Haiku pour les prompts simples, passe à Sonnet pour les refacto complexes.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 3. Installer Claude Code CLI

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Vérifier Node.js
```powershell
node --version   # ≥ 18
```

Sinon : `winget install OpenJS.NodeJS.LTS`.

### Installer Claude Code
```powershell
npm install -g @anthropic-ai/claude-code
```

Si erreur de permissions → ouvre PowerShell **en administrateur**.

### Vérifier
```powershell
claude --version
claude --help
```

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 4. Authentification

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Au premier lancement de `claude`, le CLI te propose :

### Option A — Connexion à un compte Pro/Max (browser)
1. `claude` te donne une URL.
2. Ouvre-la dans Edge/Chrome, connecte-toi à ton compte Anthropic.
3. Reviens dans PowerShell — c'est connecté.

### Option B — Clé API
```powershell
$env:ANTHROPIC_API_KEY = "sk-ant-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
```

Persistant via le profil PowerShell :
```powershell
notepad $PROFILE
# Ajoute :
$env:ANTHROPIC_API_KEY = "sk-ant-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
```

Ou en variable utilisateur Windows :
```powershell
[System.Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY", "sk-ant-...", "User")
```

> ⚠️ **Ne commit jamais** `ANTHROPIC_API_KEY` dans Git.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 5. Créer le dossier projet et l'initialiser

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```powershell
mkdir $env:USERPROFILE\projets\gestionnaire-taches-claude -Force
cd $env:USERPROFILE\projets\gestionnaire-taches-claude
git init -b main
claude
```

Une fois dans la session interactive `claude`, tape :
```
/init
```

`/init` analyse le dossier et crée un `CLAUDE.md` (mémoire du projet).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 6. Le fichier `CLAUDE.md` (mémoire du projet)

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


`CLAUDE.md` est lu par Claude à chaque session. Édite-le :

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
```powershell
python main.py
```

## Tests manuels
1. Ajouter "tâche A" → apparaît dans la liste
2. Cliquer "Faite" → préfixe "[X] "
3. Re-cliquer "Faite" → toggle off
4. Bouton "Supprimer" → enlève la sélection
5. Fermer/relancer → tâches persistantes (tasks.json)
~~~

Sauvegarde. Maintenant Claude connaît ton projet.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 7. Étape A — Squelette Tkinter (laisse Claude faire)

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


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
```powershell
dir
type main.py
python main.py
```

Une fenêtre doit s'ouvrir.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 8. Étape B à E — Construire la mini-app

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


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

> Claude va modifier plusieurs fichiers d'un coup. Après chaque étape, lance `python main.py` pour tester.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 9. Slash commands utiles

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Commande | Effet |
|----------|-------|
| `/init` | Initialise CLAUDE.md |
| `/help` | Aide |
| `/clear` | Reset la conversation (garde CLAUDE.md) |
| `/compact` | Résume l'historique (économise les tokens) |
| `/plan` | Active le mode plan (Claude propose avant d'agir) |
| `/diff` | Montre les modifs faites depuis le début |
| `/context` | Voir ce qui est dans le contexte |
| `/permissions` | Gérer les permissions |
| `/status` | État de la session |
| `/model` | Changer de modèle (Sonnet ↔ Haiku) |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 10. Mode plan, diff et permissions

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Mode plan
```
/plan
```

Claude propose **d'abord** un plan textuel, attend ton OK, puis exécute. Idéal pour les refactos importants.

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
- Auto-write
- Auto-edit
- Auto-bash

Pour un débutant : **garde tout sur "ask"**. Pour un dev pro : auto-edit OK, auto-bash dangereux.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 11. Bonus — Premier commit Git

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Tu peux demander à Claude :

```
Fais un commit Git avec un message descriptif des changements.
```

Claude va `git add`, `git commit -m "..."`, et te montrer le résultat.

Ou manuellement :
```powershell
@"
__pycache__/
*.pyc
.venv/
.env
Thumbs.db
"@ | Set-Content .gitignore

git add .
git commit -m "TP mini-app : gestionnaire de tâches Tkinter avec Claude Code"
git log --oneline
```

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Troubleshooting

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| `claude: command not found` | npm pas dans le PATH | Ferme/rouvre PowerShell |
| `401 unauthorized` | Pas connecté | Relance `claude` et fais l'auth, ou `$env:ANTHROPIC_API_KEY = ...` |
| `429 rate limited` | Trop de requêtes | Attends 1 min ; passe sur Haiku via `/model` |
| `insufficient_quota` | Crédits/abonnement épuisé | Recharge sur [console.anthropic.com/billing](https://console.anthropic.com/billing) |
| Claude refuse une commande shell | Permissions strictes | `/permissions` → autorise la commande |
| `CLAUDE.md` ignoré | Pas dans le bon dossier | Lance `claude` depuis le dossier qui contient `CLAUDE.md` |
| Fichiers modifiés sans accord | Auto-edit activé | `/permissions` → repasse `edit` sur "ask" |
| Erreur de TLS / proxy | Proxy d'entreprise | `$env:HTTPS_PROXY = "http://proxy:port"` |
| Page d'authentification ne s'ouvre pas | Browser par défaut bloqué | Copie l'URL et ouvre-la manuellement |
| Accents cassés | Console CP1252 | `chcp 65001` |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Suite

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


- [`06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md`](./06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md) — comparer avec la version locale gratuite
- [`07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md`](./07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md) — comparer avec la version cloud gratuite
- [Documentation officielle Claude Code](https://docs.claude.com/en/docs/claude-code/overview)

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>
