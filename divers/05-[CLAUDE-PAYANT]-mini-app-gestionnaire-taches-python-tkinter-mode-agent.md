# 05 — TP payant : mini application Python (Tkinter) avec **Claude Code en mode agent**

> **Public** : étudiants débutants en Python.
> **Coût** : payant (abonnement Anthropic — Claude Code officiel).
> **OS supportés** : Linux, macOS, Windows.
> **Durée** : 1 h 30 à 2 h.
> **Pré-requis** : Claude Code officiel installé (`claude` dans le PATH), Python 3.10+, Git.

Ce TP construit une mini application graphique de gestion de tâches en Python + Tkinter, en utilisant **Claude Code officiel en mode agent** : il lit, écrit et modifie `main.py` tout seul, sous votre supervision.

<details>
<summary><strong>Différence clé avec OpenClaude (version gratuite — voir fichier 06)</strong></summary>

| Critère | Claude Code officiel (ce TP) | OpenClaude + Ollama |
|---------|------------------------------|---------------------|
| Coût | abonnement Anthropic | gratuit |
| Mode | **agent** : modifie les fichiers | **chat** : vous copiez-collez |
| Slash commands | toutes fiables (`/init`, `/plan`, `/diff`, etc.) | partiellement fiables |
| Mémoire `CLAUDE.md` | chargée automatiquement | doit être lue explicitement |
| Skills (`SKILL.md`) | supportées via `~/.claude/skills/` ou `.claude/skills/` | non supportées |
| Quand le préférer | besoin de fiabilité, projet réel | apprentissage gratuit, machine sans budget cloud |

</details>

---

## Sommaire

1. [Créer le projet](#1-créer-le-projet)
2. [Ouvrir le projet avec Claude Code](#2-ouvrir-le-projet-avec-claude-code)
3. [Première commande : `/help`](#3-première-commande--help)
4. [Lister toutes les commandes : `/`](#4-lister-toutes-les-commandes--)
5. [Initialiser avec `/init`](#5-initialiser-avec-init)
6. [Demander un plan avec `/plan`](#6-demander-un-plan-avec-plan)
7. [Générer la version 1 du code](#7-générer-la-version-1-du-code)
8. [Vérifier les modifications avec `/diff`](#8-vérifier-les-modifications-avec-diff)
9. [Tester l'application](#9-tester-lapplication)
10. [Code attendu pour `main.py`](#10-code-attendu-pour-mainpy)
11. [Améliorer : sauvegarde JSON](#11-améliorer--sauvegarde-json)
12. [Surveiller le contexte avec `/context`](#12-surveiller-le-contexte-avec-context)
13. [Résumer la session avec `/compact`](#13-résumer-la-session-avec-compact)
14. [Contrôler les permissions avec `/permissions`](#14-contrôler-les-permissions-avec-permissions)
15. [Créer une skill : `/python-feature`](#15-créer-une-skill--python-feature)
16. [Créer une skill : `/explain-python`](#16-créer-une-skill--explain-python)
17. [Résumé final](#17-résumé-final)
18. [Troubleshooting](#18-troubleshooting)

---

## 1. Créer le projet

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 1.1 Créer le dossier

#### Linux / macOS (bash ou zsh)
```bash
mkdir mini-app-python-claude
cd mini-app-python-claude
```

#### Windows (PowerShell)
```powershell
mkdir mini-app-python-claude
cd mini-app-python-claude
```

### 1.2 Créer un `main.py` vide

#### Linux / macOS
```bash
touch main.py
```

#### Windows (PowerShell)
```powershell
New-Item main.py
```

> **Différence OS** : Windows n'a pas `touch` natif. PowerShell utilise `New-Item`.

### 1.3 Initialiser Git

```bash
git init
git add main.py
git commit -m "Initialisation du projet Python"
```

Identique sur les trois OS.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 2. Ouvrir le projet avec Claude Code

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Toujours dans le dossier `mini-app-python-claude`, lancez :

```bash
claude
```

Identique sur les trois OS. Vous êtes maintenant dans une session Claude Code, qui peut **lire, modifier et exécuter des commandes** dans ce dossier.

> **Critique** : Claude Code n'opère que sur le dossier dans lequel il est lancé. Vérifiez :
>
> | OS | Commande |
> |----|----------|
> | Linux / macOS | `pwd` |
> | Windows | `Get-Location` ou `pwd` |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 3. Première commande : `/help`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Dans Claude Code, tapez :

```
/help
```

L'aide s'affiche. **Règle** : les slash commands commencent toujours par `/` et servent à contrôler Claude Code (et non à exécuter du code).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 4. Lister toutes les commandes : `/`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Tapez simplement :

```
/
```

Claude Code affiche la liste complète des commandes disponibles. Réflexe à acquérir : quand vous ne vous souvenez plus, tapez `/`.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 5. Initialiser avec `/init`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Dans Claude Code :

```
/init
```

Cette commande crée un fichier `CLAUDE.md` à la racine du projet. C'est la **mémoire de projet** : Claude Code la lira automatiquement à chaque session.

Améliorez ensuite ce `CLAUDE.md` en demandant à Claude :

```
Réécris CLAUDE.md pour ce projet.

Contexte :
- mini application Python pour étudiants débutants ;
- Tkinter uniquement, fichier principal main.py ;
- code simple, lisible, commenté ;
- pas de framework web (Flask, Django, FastAPI) ;
- pas de React, pas de npm pour le projet Python ;
- pas de dépendance externe ;
- chaque modification doit être expliquée.
```

Claude modifie `CLAUDE.md` directement.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 6. Demander un plan avec `/plan`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Avant d'écrire du code, demandez un plan :

```
/plan créer une mini application Python Tkinter pour gérer une liste de tâches
```

Plan attendu :

```
1. Créer une fenêtre Tkinter
2. Ajouter un champ de saisie (Entry)
3. Ajouter un bouton Ajouter
4. Ajouter une Listbox
5. Ajouter un bouton Marquer comme terminée
6. Ajouter un bouton Supprimer
```

> **Règle** : on ne demande jamais à l'IA de coder directement. On lui demande **d'abord un plan**, puis on valide, puis on génère.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 7. Générer la version 1 du code

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Demandez à Claude Code :

```
Crée la version 1 de l'application dans main.py.

Contraintes :
- Python + Tkinter ;
- une seule fenêtre principale ;
- un Entry, trois boutons : Ajouter, Marquer comme terminée, Supprimer ;
- une Listbox ;
- code simple, commentaires pédagogiques ;
- pas de classes au début ;
- un seul fichier main.py.
```

Claude Code modifie `main.py` directement (mode agent).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 8. Vérifier les modifications avec `/diff`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


**Ne lancez pas immédiatement le programme.** Vérifiez d'abord :

```
/diff
```

Claude affiche les changements ligne par ligne.

> **Règle d'or** : on ne fait jamais confiance aveuglément à une IA. Chaque modification se relit avec `/diff` avant test.

Vous pouvez aussi utiliser `git diff` dans un autre terminal pour la même information.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 9. Tester l'application

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


#### Linux / macOS
```bash
python3 main.py
```

#### Windows (PowerShell)
```powershell
python main.py
```

Une fenêtre Tk s'ouvre. Testez :

```
1. Tapez "Acheter du lait" puis cliquez Ajouter.
2. Sélectionnez la tâche, cliquez "Marquer comme terminée".
3. Sélectionnez à nouveau, cliquez "Supprimer".
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

## 10. Code attendu pour `main.py`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


À titre de référence, voici à quoi `main.py` doit ressembler :

```python
import tkinter as tk
from tkinter import messagebox


def ajouter_tache():
    texte = entree_tache.get()
    if texte.strip() == "":
        messagebox.showwarning("Attention", "Veuillez écrire une tâche.")
        return
    liste_taches.insert(tk.END, texte)
    entree_tache.delete(0, tk.END)


def terminer_tache():
    selection = liste_taches.curselection()
    if not selection:
        messagebox.showwarning("Attention", "Veuillez sélectionner une tâche.")
        return
    index = selection[0]
    tache = liste_taches.get(index)
    if not tache.startswith("[Terminée] "):
        liste_taches.delete(index)
        liste_taches.insert(index, "[Terminée] " + tache)


def supprimer_tache():
    selection = liste_taches.curselection()
    if not selection:
        messagebox.showwarning("Attention", "Veuillez sélectionner une tâche.")
        return
    liste_taches.delete(selection[0])


fenetre = tk.Tk()
fenetre.title("Mini Gestionnaire de Tâches")
fenetre.geometry("500x400")

tk.Label(fenetre, text="Gestionnaire de tâches",
         font=("Arial", 18, "bold")).pack(pady=10)

entree_tache = tk.Entry(fenetre, width=40, font=("Arial", 12))
entree_tache.pack(pady=10)

tk.Button(fenetre, text="Ajouter", width=20,
          command=ajouter_tache).pack(pady=5)

liste_taches = tk.Listbox(fenetre, width=50, height=10, font=("Arial", 12))
liste_taches.pack(pady=10)

tk.Button(fenetre, text="Marquer comme terminée", width=25,
          command=terminer_tache).pack(pady=5)
tk.Button(fenetre, text="Supprimer", width=25,
          command=supprimer_tache).pack(pady=5)

fenetre.mainloop()
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

## 11. Améliorer : sauvegarde JSON

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Demandez à Claude Code :

```
/plan ajouter une sauvegarde des tâches dans un fichier tasks.json
```

Puis :

```
Implémente le plan dans main.py.

Règles :
- module standard json ;
- sauvegarder après ajout, suppression, marquage terminé ;
- charger au démarrage, juste après la création de la Listbox ;
- pas de classes ;
- commentaires pédagogiques.
```

Claude modifie `main.py`. Vérifiez :

```
/diff
```

Vous devez voir l'ajout de :

```
import json
def sauvegarder_taches(): ...
def charger_taches(): ...
```

Testez :

#### Linux / macOS
```bash
python3 main.py
```

#### Windows (PowerShell)
```powershell
python main.py
```

Ajoutez 3 tâches, fermez la fenêtre. Vérifiez que `tasks.json` existe à côté de `main.py`. Relancez : les 3 tâches sont là.

Committez :

```bash
git add main.py
git commit -m "Sauvegarde JSON des tâches"
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

## 12. Surveiller le contexte avec `/context`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```
/context
```

Affiche l'utilisation du contexte (taille de la conversation en tokens).

Plus la conversation est longue, plus Claude garde de tokens en mémoire. À surveiller pour les longues sessions.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 13. Résumer la session avec `/compact`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Quand `/context` montre une conversation devenue trop lourde :

```
/compact Garde seulement l'état actuel du projet, les fichiers modifiés, les décisions importantes et les prochaines étapes.
```

Claude résume tout ce qui a été dit en gardant uniquement l'essentiel pour continuer.

| Commande | Quand l'utiliser |
|----------|-----------------|
| `/context` | Vérifier l'état | 
| `/compact <consigne>` | Réduire avant que ça déborde |
| `/clear` | Repartir totalement à zéro |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 14. Contrôler les permissions avec `/permissions`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```
/permissions
```

Affiche / configure ce que Claude Code peut faire :

- lire des fichiers ;
- modifier des fichiers ;
- exécuter des commandes shell.

C'est essentiel : Claude Code agit directement sur votre projet. À ajuster selon votre niveau de confiance et la sensibilité du dossier.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 15. Créer une skill : `/python-feature`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


> **Skill** = procédure réutilisable. Définie dans un fichier `SKILL.md`, elle s'invoque par `/nom-de-la-skill`.

### 15.1 Créer le dossier

#### Linux / macOS
```bash
mkdir -p .claude/skills/python-feature
touch .claude/skills/python-feature/SKILL.md
```

#### Windows (PowerShell)
```powershell
New-Item -ItemType Directory -Force .claude\skills\python-feature
New-Item .claude\skills\python-feature\SKILL.md
```

> **Différence OS** : `mkdir -p` (création récursive sans erreur si existe) sur Linux/Mac. Sur PowerShell : `New-Item -ItemType Directory -Force`.

### 15.2 Contenu de `SKILL.md`

```markdown
---
description: Ajoute une fonctionnalité Python simple dans une application Tkinter pour étudiants débutants.
---

Tu dois ajouter une fonctionnalité à une application Python Tkinter.

Demande de l'utilisateur :

$ARGUMENTS

Méthode obligatoire :

1. Lire main.py.
2. Proposer un mini-plan simple.
3. Modifier le code prudemment.
4. Garder le code compréhensible pour débutants.
5. Pas de dépendance externe.
6. Commentaires pédagogiques.
7. Expliquer les changements.
8. Donner la commande de test.
```

### 15.3 Utiliser la skill

```
/python-feature ajouter un bouton qui efface toutes les tâches après confirmation
```

Claude Code charge le `SKILL.md`, applique la procédure et modifie `main.py`. Vérifiez avec `/diff`.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 16. Créer une skill : `/explain-python`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 16.1 Créer le dossier et le fichier

#### Linux / macOS
```bash
mkdir -p .claude/skills/explain-python
touch .claude/skills/explain-python/SKILL.md
```

#### Windows (PowerShell)
```powershell
New-Item -ItemType Directory -Force .claude\skills\explain-python
New-Item .claude\skills\explain-python\SKILL.md
```

### 16.2 Contenu

```markdown
---
description: Explique du code Python ligne par ligne pour des étudiants débutants.
---

Explique le code demandé de manière très pédagogique.

Code ou fichier à expliquer :

$ARGUMENTS

Structure obligatoire :

1. Rôle général du code.
2. Imports.
3. Variables importantes.
4. Fonctions.
5. Événements Tkinter.
6. Erreurs fréquentes à éviter.
7. Résumé final simple.
```

### 16.3 Utiliser

```
/explain-python main.py
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

## 17. Résumé final

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Commande | Rôle |
|----------|------|
| `/help` | Afficher l'aide |
| `/` | Lister toutes les commandes |
| `/init` | Créer `CLAUDE.md` |
| `/plan <objectif>` | Demander un plan avant de coder |
| `/diff` | Voir les modifications |
| `/context` | Voir l'utilisation du contexte |
| `/compact <consigne>` | Résumer la session |
| `/permissions` | Contrôler les actions autorisées |
| `/python-feature <demande>` | Ajouter une fonctionnalité (skill perso) |
| `/explain-python <fichier>` | Expliquer du code (skill perso) |

> Claude Code n'est pas qu'un générateur de code. C'est un **outil de pilotage** d'un vrai processus de développement : planifier → coder → vérifier → améliorer → expliquer.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 18. Troubleshooting

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Claude Code

| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| `claude: command not found` | Pas installé ou PATH non rechargé | Réinstaller, fermer/rouvrir le terminal |
| `/init` ne crée rien | Pas dans le bon dossier | `cd` dans le projet puis relancer |
| `/diff` vide après une demande | Claude n'a pas modifié de fichier | Reformuler la demande, vérifier `/permissions` |
| Quotas / rate limit | Plan Anthropic dépassé | Patienter ou upgrader |
| Skill `/python-feature` non reconnue | Fichier `SKILL.md` mal placé | Vérifier `.claude/skills/python-feature/SKILL.md` à la racine du projet |

### Python / Tkinter

| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| `ModuleNotFoundError: No module named 'tkinter'` | Tkinter pas installé (Linux) | `sudo apt install python3-tk` (Debian/Ubuntu) ; `sudo dnf install python3-tkinter` (Fedora) ; `brew install python-tk` (macOS) |
| `python: command not found` (Linux/Mac) | Binaire `python3` | Utiliser `python3 main.py` |
| `python` ouvre le Microsoft Store (Windows) | Alias système | Réinstaller Python depuis [python.org](https://www.python.org), cocher *Add to PATH* |
| Fenêtre se ferme aussitôt | Oubli de `fenetre.mainloop()` | Ajouter la ligne en bas |

### Git

| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| `Author identity unknown` | Identité non configurée | `git config --global user.name "..."` puis `user.email "..."` |
| Accents cassés dans `git diff` (Windows) | Console CP1252 | `chcp 65001` avant la commande |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>
