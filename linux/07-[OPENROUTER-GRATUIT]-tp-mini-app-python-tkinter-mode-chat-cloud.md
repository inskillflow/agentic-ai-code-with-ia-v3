# 07 — TP mini-app Python/Tkinter sur **Linux** : OpenClaude + OpenRouter (mode chat, cloud gratuit)

> **Public** : étudiants débutants en Python.
> **Coût** : 100 % gratuit (modèles `:free` d'OpenRouter).
> **OS** : Linux.
> **Durée** : 1 h.
> **Avantage clé** : pas besoin de GPU local, modèles plus puissants que ce que tu peux faire tourner sur ta machine.
> **Pré-requis** :
> - [Tuto 02 — OpenClaude installé](./02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md)
> - [Tuto 03 — OpenRouter configuré (clé API)](./03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md)
> - Python 3.9+ avec Tkinter

> Pour macOS → [`../macos/07-[OPENROUTER-GRATUIT]-...`](../macos/07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md).
> Pour Windows → [`../windows/07-[OPENROUTER-GRATUIT]-...`](../windows/07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md).

> **Quelle différence avec le tuto 06 ?**
> - Le tuto 06 utilise Ollama **local** (pas d'internet, pas de quota).
> - Ce tuto 07 utilise OpenRouter **cloud** (internet requis, quotas, mais modèles 70B-405B).
> - Le résultat de l'IA est **bien meilleur** ici si tu n'as pas un bon GPU.

---

## Sommaire

1. [Quand préférer OpenRouter à Ollama ?](#1-quand-préférer-openrouter-à-ollama-)
2. [Vérifier ton accès OpenRouter](#2-vérifier-ton-accès-openrouter)
3. [Préparer l'environnement Python](#3-préparer-lenvironnement-python)
4. [Créer le dossier de projet](#4-créer-le-dossier-de-projet)
5. [Configurer OpenClaude pour OpenRouter](#5-configurer-openclaude-pour-openrouter)
6. [Lancer OpenClaude](#6-lancer-openclaude)
7. [Étape A à E — Construction de la mini-app](#7-étape-a-à-e--construction-de-la-mini-app)
8. [Astuces pour économiser tes quotas `:free`](#8-astuces-pour-économiser-tes-quotas-free)
9. [Sécurité de la clé API](#9-sécurité-de-la-clé-api)
10. [Bonus — Premier commit Git](#10-bonus--premier-commit-git)
11. [Troubleshooting](#troubleshooting)
12. [Suite](#suite)

---

## 1. Quand préférer OpenRouter à Ollama ?

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Cas | Préfère |
|-----|---------|
| Tu n'as pas de GPU NVIDIA | **OpenRouter** (Llama 405B sur cloud > Llama 7B sur ton CPU) |
| Tu fais le TP en classe avec internet | **OpenRouter** |
| Tu veux la meilleure qualité de code IA gratuite | **OpenRouter** (Llama 3.3 70B > Qwen 7B local) |
| Tu travailles sans internet (train, avion) | **Ollama** |
| Tu fais des centaines de prompts d'affilée | **Ollama** (pas de quota) |
| Tu manipules du code confidentiel | **Ollama** (rien ne sort) |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 2. Vérifier ton accès OpenRouter

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
echo $OPENAI_API_KEY     # doit commencer par sk-or-v1-
echo $OPENAI_BASE_URL    # doit être https://openrouter.ai/api/v1
```

Test direct :

```bash
curl -s https://openrouter.ai/api/v1/models \
    -H "Authorization: Bearer $OPENAI_API_KEY" | head -c 200
```

Tu dois voir un JSON listant les modèles. Si erreur 401 → clé invalide, retour au [tuto 03](./03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 3. Préparer l'environnement Python

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
python3 --version
python3 -c "import tkinter; print('Tkinter OK')"
```

Si Tkinter manque :
- Ubuntu/Debian : `sudo apt install python3-tk`
- Fedora : `sudo dnf install python3-tkinter`
- Arch : `sudo pacman -S tk`

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 4. Créer le dossier de projet

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
mkdir -p ~/projets/gestionnaire-taches-cloud
cd ~/projets/gestionnaire-taches-cloud
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

## 5. Configurer OpenClaude pour OpenRouter

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Pour ce TP, on choisit un modèle qui code bien :

```bash
export OPENAI_API_KEY="sk-or-v1-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
export OPENAI_BASE_URL="https://openrouter.ai/api/v1"
export OPENAI_MODEL="qwen/qwen-2.5-coder-32b-instruct:free"
```

> Alternatives selon ton goût : `meta-llama/llama-3.3-70b-instruct:free`, `deepseek/deepseek-chat-v3:free`, `google/gemini-2.0-flash-exp:free`.

Vérifie :

```bash
echo "$OPENAI_BASE_URL"
echo "$OPENAI_MODEL"
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

## 6. Lancer OpenClaude

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
openclaude
```

Tape :

```
Bonjour ! Quel modèle exact es-tu et qui t'a entraîné ?
```

Tu dois voir une réponse cohérente identifiant le modèle (Qwen, Llama, DeepSeek...). Sinon → retour étape 5.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 7. Étape A à E — Construction de la mini-app

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


> Le déroulé est **identique au [tuto 06](./06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md)** mais avec un meilleur modèle, donc tu peux te permettre des prompts plus ambitieux.

### Étape A — Squelette Tkinter

```
Donne-moi le code Python d'une fenêtre Tkinter 400x500 intitulée
"Mon Gestionnaire de Tâches" avec un titre "Mes tâches".
Pattern : classe TaskApp, main guard, pas de commentaires inutiles.
```

Crée `main.py`, colle, lance :

```bash
python3 main.py
```

### Étape B — Ajouter une tâche

```
À TaskApp, ajoute :
- un Entry pour saisir
- un bouton "Ajouter"
- une Listbox affichant les tâches
Liste en mémoire dans self.tasks.
Vide l'Entry après ajout.
Réécris seulement les méthodes modifiées.
```

### Étape C — Supprimer

```
Bouton "Supprimer" qui retire la tâche sélectionnée.
Si rien n'est sélectionné, ne fais rien (pas d'erreur).
```

### Étape D — Marquer comme faite (toggle)

```
Bouton "Faite" qui préfixe la tâche par "[X] ".
Si déjà préfixée, retire le préfixe (toggle).
```

### Étape E — Persistance JSON

```
Sauvegarde self.tasks dans tasks.json à chaque modification.
Au démarrage, charge tasks.json s'il existe (vide si JSONDecodeError).
```

### Bonus (qu'on n'osait pas avec Qwen 7B)

Avec un modèle 32B, tu peux demander plus :

```
Refactor ma classe TaskApp pour séparer la logique métier
(ajouter/supprimer/toggle/sauvegarder) de l'UI Tkinter.
Crée une classe TaskRepository qui gère le JSON,
et garde TaskApp pour l'UI uniquement.
Donne-moi les deux classes.
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

## 8. Astuces pour économiser tes quotas `:free`

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


OpenRouter limite à ~20 req/min et ~200 req/jour sur les modèles `:free`.

**À faire** :
- ✅ Pose **une question complète** plutôt que 10 petites
- ✅ Joins le code pertinent à ta question (évite à OpenClaude de redemander)
- ✅ Utilise `/clear` quand tu changes de sujet (évite l'historique inutile)
- ✅ Pour les essais rapides, garde Ollama local en backup

**À éviter** :
- ❌ Demander « est-ce que tu peux ? » puis « alors fais-le »
- ❌ Coller 500 lignes de code juste pour demander une virgule

Si tu te fais bloquer (`429 Too Many Requests`), bascule temporairement sur Ollama (cf. tuto 06) :

```bash
export OPENAI_API_KEY=ollama
export OPENAI_BASE_URL=http://127.0.0.1:11434/v1
export OPENAI_MODEL=qwen2.5-coder:7b
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

## 9. Sécurité de la clé API

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


> ⚠️ Ta clé `sk-or-v1-...` est un secret. Ne la commit **jamais** dans Git.

Dans ton dossier projet :

```bash
cat > .gitignore <<'EOF'
__pycache__/
*.pyc
.venv/
.env
EOF
```

Si tu utilises un `.env` projet :
```bash
cat > .env <<'EOF'
OPENAI_API_KEY=sk-or-v1-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
OPENAI_BASE_URL=https://openrouter.ai/api/v1
OPENAI_MODEL=qwen/qwen-2.5-coder-32b-instruct:free
EOF
```

Vérifie qu'il est bien ignoré :
```bash
git status   # tu ne dois pas voir .env
```

Si tu commit accidentellement ta clé, **régénère-la immédiatement** sur [openrouter.ai/keys](https://openrouter.ai/keys).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 10. Bonus — Premier commit Git

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
cd ~/projets/gestionnaire-taches-cloud
git init -b main
# .gitignore déjà créé en section 9

git add .
git status   # vérifie absence de .env
git commit -m "TP mini-app cloud (OpenRouter free) : gestionnaire de tâches Tkinter"
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
| `401 Unauthorized` | Clé API absente/erronée | Vérifie `echo $OPENAI_API_KEY` ; régénère sur [openrouter.ai/keys](https://openrouter.ai/keys) |
| `404 model not found` | Modèle plus disponible | Va sur [openrouter.ai/models?max_price=0](https://openrouter.ai/models?max_price=0) prendre le nom à jour |
| `429 Too Many Requests` | Rate limit `:free` dépassé | Attends 1 min, ou bascule sur Ollama (cf. tuto 06) |
| `402 Payment Required` | Modèle payant sans crédits | Repasse à un `:free` |
| Réponse en anglais alors que tu écris en français | Modèle moins multilingue | Utilise `meta-llama/llama-3.3-70b-instruct:free` ou `qwen/...` |
| `ModuleNotFoundError: tkinter` | Tkinter pas installé | `sudo apt install python3-tk` |
| Connexion lente | Internet faible | Bascule sur Ollama (cf. tuto 06) |

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


- [`06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md`](./06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md) — la version locale (Ollama, sans quota, sans internet)
- [`08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md`](./08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md) — la version « pro » avec Claude Code en mode agent

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>
