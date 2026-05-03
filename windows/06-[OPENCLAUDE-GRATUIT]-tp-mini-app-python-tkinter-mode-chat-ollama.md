# 06 — TP mini-app Python/Tkinter sur **Windows** : OpenClaude + Ollama (mode chat)

> **Public** : étudiants débutants en Python.
> **Coût** : 100 % gratuit (Ollama local).
> **OS** : Windows 10 / 11.
> **Durée** : 1 h à 1 h 30.
> **Pré-requis** :
> - [Tuto 01 — Ollama installé](./01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md)
> - [Tuto 02 — OpenClaude installé et branché sur Ollama](./02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md)
> - Python 3.9+ avec Tkinter (inclus par défaut)
> - PowerShell 7 et un éditeur (VS Code recommandé)

> Pour Linux → [`../linux/06-[OPENCLAUDE-GRATUIT]-...`](../linux/06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md).
> Pour macOS → [`../macos/06-[OPENCLAUDE-GRATUIT]-...`](../macos/06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md).

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

Avec un LLM local (Ollama Qwen 7B), le tool-calling d'OpenClaude est **peu fiable**. On utilise donc OpenClaude comme un **chatbot de code** :

| Toi | OpenClaude |
|-----|------------|
| Décris ce que tu veux | Te donne du code à coller |
| **Tu copies/colles** dans ton fichier | (ne touche pas tes fichiers) |
| Tu lances `python` | (ne lance rien) |
| Tu lui dis l'erreur | Te propose un correctif |

C'est parfait pour apprendre : tu **comprends** chaque ligne, tu **lis** chaque erreur. Pas de magie noire.

[↑ Sommaire](#sommaire)

---

## 2. Préparer l'environnement Python

### Vérifier Python et Tkinter

Dans **PowerShell** :
```powershell
python --version
python -c "import tkinter; print('Tkinter OK')"
```

Si Python n'est pas installé :
```powershell
winget install Python.Python.3.12
```

> **Important au moment de l'install Python** : coche bien « **Add Python to PATH** » et garde **Tcl/Tk** dans les options (coché par défaut). Sans Tcl/Tk, pas de Tkinter.

### Vérifier Ollama

```powershell
ollama list
curl.exe http://127.0.0.1:11434/api/tags
```

Si pas de réponse : relance Ollama depuis le menu Démarrer.

### Vérifier OpenClaude est branché sur Ollama

```powershell
echo $env:OPENAI_BASE_URL
echo $env:OPENAI_MODEL
```

Si vide :
```powershell
$env:OPENAI_API_KEY = "ollama"
$env:OPENAI_BASE_URL = "http://127.0.0.1:11434/v1"
$env:OPENAI_MODEL = "qwen2.5-coder:7b"   # ou autre modèle dans `ollama list`
```

> **Piège Windows** : utilise toujours `127.0.0.1`, jamais `localhost`.

[↑ Sommaire](#sommaire)

---

## 3. Créer le dossier de projet

```powershell
mkdir $env:USERPROFILE\projets\gestionnaire-taches -Force
cd $env:USERPROFILE\projets\gestionnaire-taches
```

[↑ Sommaire](#sommaire)

---

## 4. Lancer OpenClaude

Dans le dossier du projet :

```powershell
openclaude
```

Tu vois un prompt interactif. Tape :

```
bonjour, dis-moi en une ligne quel modèle tu utilises
```

Si tu obtiens une réponse → c'est branché. Sinon, retour à l'**étape 2**.

[↑ Sommaire](#sommaire)

---

## 5. Étape A — Squelette Tkinter

### Demande à OpenClaude

```
Donne-moi le code Python d'une fenêtre Tkinter minimale 400x500 pixels
intitulée "Mon Gestionnaire de Tâches" qui affiche juste un titre "Mes tâches".
Utilise le pattern d'une classe TaskApp et un main guard if __name__ == "__main__".
N'ajoute aucun commentaire qui décrit le code ligne par ligne.
```

### Crée le fichier

Ouvre VS Code dans le dossier :
```powershell
code .
```

Crée `main.py`, colle le code reçu d'OpenClaude. Sauvegarde (`Ctrl+S`).

### Lance

Depuis PowerShell :
```powershell
python main.py
```

Si une fenêtre s'ouvre → ✅ Étape A validée.

> Sur Windows, `python` et `py` fonctionnent tous les deux. `py` permet de choisir une version : `py -3.12 main.py`.

### Si erreur

Copie le message d'erreur dans OpenClaude :

```
J'ai cette erreur quand je lance python main.py :
[colle l'erreur]
Que dois-je corriger ?
```

[↑ Sommaire](#sommaire)

---

## 6. Étape B — Ajouter une tâche

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

Remplace dans `main.py`, sauvegarde.

### Test

```powershell
python main.py
```

Tape « acheter du pain », clique « Ajouter ». La tâche apparaît.

[↑ Sommaire](#sommaire)

---

## 7. Étape C — Supprimer une tâche

### Demande

```
Ajoute un bouton "Supprimer" qui retire la tâche sélectionnée dans la Listbox.
Si rien n'est sélectionné, n'affiche pas d'erreur, ne fais rien.
```

Applique, teste.

[↑ Sommaire](#sommaire)

---

## 8. Étape D — Marquer comme faite

### Demande

```
Ajoute un bouton "Faite". Quand on clique, la tâche sélectionnée est préfixée
par "[X] ". Si elle est déjà préfixée, retire le préfixe (toggle).
```

Applique, teste.

[↑ Sommaire](#sommaire)

---

## 9. Étape E — Persistance JSON

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

```powershell
type tasks.json
```

[↑ Sommaire](#sommaire)

---

## 10. Workflow type avec OpenClaude

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

> **Astuce** : si OpenClaude te donne du code qui « écrase » trop de choses, arrête et redemande : *« réécris uniquement la méthode add_task, pas toute la classe »*.

[↑ Sommaire](#sommaire)

---

## 11. Bonus — Premier commit Git

```powershell
cd $env:USERPROFILE\projets\gestionnaire-taches
git init -b main

@"
__pycache__/
*.pyc
.venv/
.env
Thumbs.db
"@ | Set-Content .gitignore

git add .
git status
git commit -m "TP mini-app : gestionnaire de tâches Tkinter avec persistance JSON"
git log --oneline
```

[↑ Sommaire](#sommaire)

---

## Troubleshooting

| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| `'python' is not recognized` | Python pas dans le PATH | Réinstalle Python en cochant « Add to PATH », ou redémarre PowerShell |
| `ModuleNotFoundError: No module named 'tkinter'` | Tcl/Tk décoché à l'install | Réinstalle Python (Modify → coche tcl/tk) |
| Encodage cassé dans la console | Console CP1252 | `chcp 65001` avant la commande |
| OpenClaude génère du code Python 2 | Modèle ancien | Précise : *« en Python 3.9+ »* |
| OpenClaude réécrit tout le fichier à chaque demande | Pas de contraintes dans le prompt | Demande : *« seulement la méthode X »* |
| `localhost` ne marche pas mais `127.0.0.1` oui | Résolution IPv6 vs IPv4 | Toujours `127.0.0.1` |
| `tasks.json` corrompu | Crash pendant sauvegarde | `Remove-Item tasks.json` puis relance |
| Police floue sur écran 4K | High DPI | Ajoute dans `main.py` : `from ctypes import windll; windll.shcore.SetProcessDpiAwareness(1)` (avant `Tk()`) |

### Cheatsheet OpenClaude

| Commande | Effet |
|----------|-------|
| `/help` | Aide |
| `/clear` | Reset la conversation |
| `/provider` | Change le backend |
| `Ctrl+C` puis `Ctrl+C` | Quitte OpenClaude |

[↑ Sommaire](#sommaire)

---

## Suite

- [`07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md`](./07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md) — refais le TP avec OpenRouter cloud (modèle plus puissant, sans GPU)
- [`08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md`](./08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md) — refais le TP avec Claude Code officiel en mode agent

[↑ Sommaire](#sommaire)
