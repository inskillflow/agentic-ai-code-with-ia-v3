# 06 — TP mini-app Python/Tkinter sur **Linux** : OpenClaude + Ollama (mode chat)

> **Public** : étudiants débutants en Python.
> **Coût** : 100 % gratuit (Ollama local).
> **OS** : Linux.
> **Durée** : 1 h à 1 h 30.
> **Pré-requis** :
> - [Tuto 01 — Ollama installé](./01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md)
> - [Tuto 02 — OpenClaude installé et branché sur Ollama](./02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md)
> - Python 3.9+ avec Tkinter
> - Un terminal et un éditeur (VS Code, nano, etc.)

> Pour macOS → [`../macos/06-[OPENCLAUDE-GRATUIT]-...`](../macos/06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md).
> Pour Windows → [`../windows/06-[OPENCLAUDE-GRATUIT]-...`](../windows/06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md).

---

## Sommaire

1. [Comprendre le mode « chat » d'OpenClaude](#1-comprendre-le-mode--chat--dopenclaude)
2. [Préparer l'environnement Python](#2-préparer-lenvironnement-python)
3. [Créer le dossier de projet](#3-créer-le-dossier-de-projet)
4. [Lancer OpenClaude](#4-lancer-openclaude)
5. [Étape A — Squelette Tkinter](#5-étape-a--squelette-tkinter)
6. [Étape B — Ajouter une tâche](#6-étape-b--ajouter-une-tâche)
7. [Étape C — Supprimer une tâche](#7-étape-c--supprimer-une-tâche)
8. [Étape D — Marquer comme faite](#8-étape-d--marquer-comme-faite)
9. [Étape E — Persistance JSON](#9-étape-e--persistance-json)
10. [Workflow type avec OpenClaude](#10-workflow-type-avec-openclaude)
11. [Bonus — Premier commit Git](#11-bonus--premier-commit-git)
12. [Troubleshooting](#troubleshooting)
13. [Suite](#suite)

---

## 1. Comprendre le mode « chat » d'OpenClaude

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Avec un LLM local (Ollama Qwen 7B), le tool-calling d'OpenClaude est **peu fiable**. On utilise donc OpenClaude comme un **chatbot de code** :

| Toi | OpenClaude |
|-----|------------|
| Décris ce que tu veux | Te donne du code à coller |
| **Tu copies/colles** dans ton fichier | (ne touche pas tes fichiers) |
| Tu lances `python` | (ne lance rien) |
| Tu lui dis l'erreur | Te propose un correctif |

C'est parfait pour apprendre : tu **comprends** chaque ligne, tu **lis** chaque erreur. Pas de magie noire.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 2. Préparer l'environnement Python

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Vérifier Python et Tkinter

```bash
python3 --version
python3 -c "import tkinter; print('Tkinter OK')"
```

Si Tkinter manque :
- Ubuntu/Debian : `sudo apt install python3-tk`
- Fedora : `sudo dnf install python3-tkinter`
- Arch : `sudo pacman -S tk`

### Vérifier Ollama

```bash
ollama list
curl http://127.0.0.1:11434/api/tags
```

Si pas de réponse : `sudo systemctl start ollama`.

### Vérifier OpenClaude est branché sur Ollama

```bash
echo "$OPENAI_BASE_URL"
echo "$OPENAI_MODEL"
```

Si vide :
```bash
export OPENAI_API_KEY=ollama
export OPENAI_BASE_URL=http://127.0.0.1:11434/v1
export OPENAI_MODEL=qwen2.5-coder:7b   # ou autre modèle dans `ollama list`
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

## 3. Créer le dossier de projet

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
mkdir -p ~/projets/gestionnaire-taches
cd ~/projets/gestionnaire-taches
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

## 4. Lancer OpenClaude

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Dans le dossier du projet :

```bash
openclaude
```

Tu vois un prompt interactif. Tape :

```
bonjour, dis-moi en une ligne quel modèle tu utilises
```

Si tu obtiens une réponse → c'est branché. Sinon, retour à l'**étape 2**.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 5. Étape A — Squelette Tkinter

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Demande à OpenClaude

```
Donne-moi le code Python d'une fenêtre Tkinter minimale 400x500 pixels
intitulée "Mon Gestionnaire de Tâches" qui affiche juste un titre "Mes tâches".
Utilise le pattern d'une classe TaskApp et un main guard if __name__ == "__main__".
N'ajoute aucun commentaire qui décrit le code ligne par ligne.
```

### Crée le fichier

Dans un autre terminal (ou ouvre VS Code dans le dossier `~/projets/gestionnaire-taches`) :

```bash
nano main.py
```

Colle le code reçu d'OpenClaude. Sauvegarde (`Ctrl+O`, `Enter`, `Ctrl+X`).

### Lance

```bash
python3 main.py
```

Si une fenêtre s'ouvre → ✅ Étape A validée.

### Si erreur

Copie le message d'erreur dans OpenClaude :

```
J'ai cette erreur quand je lance python3 main.py :
[colle l'erreur]
Que dois-je corriger ?
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

## 6. Étape B — Ajouter une tâche

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Demande

```
Ajoute à TaskApp :
- un Entry pour saisir le nom d'une tâche
- un bouton "Ajouter"
- une Listbox qui affiche les tâches ajoutées
Garde la liste en mémoire dans self.tasks (liste de strings).
Vide l'Entry après chaque ajout.
Ne réécris que les méthodes modifiées, en disant clairement quelle méthode remplacer.
```

### Applique le code

OpenClaude te répond avec une nouvelle classe complète OU des méthodes à intégrer. Remplace dans `main.py`.

### Test

```bash
python3 main.py
```

Tape « acheter du pain », clique « Ajouter ». La tâche apparaît.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 7. Étape C — Supprimer une tâche

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Demande

```
Ajoute un bouton "Supprimer" qui retire la tâche sélectionnée dans la Listbox.
Si rien n'est sélectionné, n'affiche pas d'erreur, ne fais rien.
```

Applique, teste.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 8. Étape D — Marquer comme faite

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Demande

```
Ajoute un bouton "Faite". Quand on clique, la tâche sélectionnée est préfixée
par "[X] ". Si elle est déjà préfixée, retire le préfixe (toggle).
```

Applique, teste. Tu dois pouvoir marquer/démarquer plusieurs fois la même tâche.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 9. Étape E — Persistance JSON

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Demande

```
Sauvegarde self.tasks dans un fichier "tasks.json" à chaque modification
(ajout, suppression, toggle). Au démarrage, charge tasks.json s'il existe,
sinon démarre avec une liste vide.
Gère le cas où le fichier existe mais est corrompu (JSONDecodeError) :
dans ce cas, démarre vide.
```

Applique, teste : ferme l'app, relance — les tâches doivent persister.

Vérifie le fichier :

```bash
cat tasks.json
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

## 10. Workflow type avec OpenClaude

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Demande de fonctionnalité

```
Je veux ajouter X. Donne-moi seulement la méthode/classe à modifier.
Ne réécris pas tout le fichier.
```

### Demande d'explication

```
Explique-moi en français simple à quoi sert cette ligne :
[colle la ligne]
```

### Demande de débuggage

```
Quand je clique sur "Supprimer" sans rien sélectionner, j'ai cette erreur :
[colle l'erreur]
Comment l'éviter proprement ?
```

### Demande de refacto

```
Voici ma méthode add_task. Peux-tu la simplifier ?
[colle la méthode]
```

> **Astuce** : si OpenClaude te donne du code qui « écrase » trop de choses, arrête et redemande : *« réécris uniquement la méthode add_task, pas toute la classe »*.

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


```bash
cd ~/projets/gestionnaire-taches
git init -b main

cat > .gitignore <<'EOF'
__pycache__/
*.pyc
.venv/
.env
EOF

git add .
git status
git commit -m "TP mini-app : gestionnaire de tâches Tkinter avec persistance JSON"
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
| `ModuleNotFoundError: No module named 'tkinter'` | Tkinter pas installé | `sudo apt install python3-tk` (Ubuntu/Debian) |
| OpenClaude génère du code Python 2 (`print "..."`) | Modèle ancien | Précise : *« en Python 3.9+ »* |
| OpenClaude réécrit tout le fichier à chaque demande | Pas de contraintes dans le prompt | Demande : *« seulement la méthode X, pas toute la classe »* |
| Le code ne marche pas du premier coup | Hallucination LLM | Copie l'erreur dans OpenClaude, demande un correctif |
| Tu perds le fil dans le chat | Trop d'historique | `/clear` dans OpenClaude pour repartir |
| OpenClaude oublie le contexte | Limite du modèle local 7B | Reformule avec tout le code en pièce jointe à la demande |
| `tasks.json` corrompu | Crash pendant sauvegarde | Supprime-le : `rm tasks.json` puis relance |

### Cheatsheet OpenClaude

| Commande | Effet |
|----------|-------|
| `/help` | Aide |
| `/clear` | Reset la conversation |
| `/provider` | Change le backend (Ollama → LM Studio par exemple) |
| `Ctrl+C` puis `Ctrl+C` | Quitte OpenClaude |

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


- [`07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md`](./07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md) — refais le même TP mais avec OpenRouter cloud (modèle plus puissant, sans GPU)
- [`08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md`](./08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md) — refais le même TP mais avec Claude Code officiel en mode agent (le code se génère tout seul)

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>
