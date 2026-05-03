# 06 — TP gratuit : mini application Python (Tkinter) avec **OpenClaude en mode chat**

> **Public** : étudiants débutants en Python.
> **Coût** : 100 % gratuit (Ollama local).
> **OS supportés** : Linux, macOS, Windows.
> **Durée** : 2 à 3 h.
> **Pré-requis** : avoir suivi `01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md` et `02-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md`.

Ce TP construit une mini application graphique de gestion de tâches en Python + Tkinter, en utilisant **OpenClaude branché sur un modèle local Ollama**.

<details>
<summary><strong>Différence clé avec Claude Code officiel (à lire en premier)</strong></summary>

| Critère | Claude Code officiel (payant) | OpenClaude + Ollama (ce TP) |
|---------|-------------------------------|------------------------------|
| Coût | abonnement Anthropic | gratuit |
| Modèle | Claude (cloud Anthropic) | Qwen2.5-coder, Gemma, Llama (local) |
| Mode | **agent** : lit, écrit, exécute des fichiers tout seul | **chat** : il propose du code, **vous le copiez-collez** |
| Fiabilité du tool-calling | très haute | instable avec Ollama, souvent inutilisable |
| Slash commands | `/init`, `/diff`, `/plan`, etc. fonctionnent | `/help`, `/provider`, `/clear` souvent OK ; `/init`, `/diff`, `/plan` peu fiables |
| Mémoire `CLAUDE.md` | chargée automatiquement | doit être lue explicitement |
| Skills (`SKILL.md`) | supportées | non supportées → on utilise `procedures/*.md` à la place |

**Conséquence pratique** : dans ce TP, vous traitez OpenClaude **comme ChatGPT dans le terminal**. Vous lui posez une question, il répond du code, vous **copiez-collez** ce code dans `main.py` à la main. Aucun fichier n'est modifié automatiquement.

</details>

---

## Sommaire

1. [Contrat pédagogique : OpenClaude en mode chat](#1-contrat-pédagogique--openclaude-en-mode-chat)
2. [Vérifier les pré-requis (Python, Tkinter, Git)](#2-vérifier-les-pré-requis-python-tkinter-git)
3. [Créer le projet et l'initialiser avec Git](#3-créer-le-projet-et-linitialiser-avec-git)
4. [Lancer OpenClaude dans le bon dossier](#4-lancer-openclaude-dans-le-bon-dossier)
5. [Configurer OpenClaude pour utiliser Ollama](#5-configurer-openclaude-pour-utiliser-ollama)
6. [Découvrir les slash commands utiles](#6-découvrir-les-slash-commands-utiles)
7. [Pourquoi on n'utilise pas `/init`](#7-pourquoi-on-nutilise-pas-init)
8. [Créer `CLAUDE.md` à la main](#8-créer-claudemd-à-la-main)
9. [Demander un plan à OpenClaude](#9-demander-un-plan-à-openclaude)
10. [Demander la version 1 du code et la coller dans `main.py`](#10-demander-la-version-1-du-code-et-la-coller-dans-mainpy)
11. [Comprendre le code généré](#11-comprendre-le-code-généré)
12. [Tester et committer la version 1](#12-tester-et-committer-la-version-1)
13. [Amélioration : bouton « Effacer toutes les tâches »](#13-amélioration--bouton--effacer-toutes-les-tâches-)
14. [Amélioration : sauvegarde JSON persistante](#14-amélioration--sauvegarde-json-persistante)
15. [Procédures réutilisables (équivalent local des skills)](#15-procédures-réutilisables-équivalent-local-des-skills)
16. [Tableau comparatif final](#16-tableau-comparatif-final)
17. [À retenir](#17-à-retenir)
18. [Troubleshooting](#18-troubleshooting)

---

## 1. Contrat pédagogique : OpenClaude en mode chat

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Avant de commencer, posez-vous bien ce contrat dans la tête :

| Ce que ce TP attend | Ce que ce TP n'attend pas |
|---------------------|---------------------------|
| Vous **lisez** les réponses d'OpenClaude. | OpenClaude n'écrira **pas** dans `main.py` à votre place. |
| Vous **copiez-collez** le code proposé dans `main.py`. | Vous n'attendez pas que des fichiers apparaissent tout seuls. |
| Vous **vérifiez** chaque modification avec `git diff`. | Vous ne faites pas confiance aveuglément au code généré. |
| Vous **demandez d'abord un plan**, puis le code. | Vous ne dites pas « code-moi tout d'un coup ». |

> **Pourquoi ce mode chat ?** Avec un modèle local (Ollama), le « tool-calling » qui permet à OpenClaude de modifier des fichiers tout seul **n'est pas fiable**. Le modèle se trompe d'outil, abandonne au milieu, ou écrit n'importe où. Le mode copier-coller est plus lent mais **toujours fiable** et plus pédagogique.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 2. Vérifier les pré-requis (Python, Tkinter, Git)

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 2.1 Python

#### Linux (bash)
```bash
python3 --version
```

#### macOS (zsh ou bash)
```bash
python3 --version
```

#### Windows (PowerShell)
```powershell
python --version
```

Vous devez voir au moins `Python 3.10`. Sinon, installez-le depuis [python.org](https://www.python.org/downloads/) (Windows/macOS) ou via votre gestionnaire de paquets (Linux).

### 2.2 Tkinter

Tkinter est livré avec Python sur Windows et macOS, mais **pas toujours sur Linux**.

#### Linux (bash)
```bash
python3 -m tkinter
# Si erreur "No module named '_tkinter'" :
sudo apt install python3-tk          # Debian / Ubuntu
sudo dnf install python3-tkinter     # Fedora
sudo pacman -S tk                    # Arch
```

#### macOS (zsh ou bash)
```bash
python3 -m tkinter
# Si erreur, réinstaller via Homebrew :
brew install python-tk
```

#### Windows (PowerShell)
```powershell
python -m tkinter
```

Une petite fenêtre Tk doit s'ouvrir. Fermez-la.

### 2.3 Git

| OS | Commande de vérification |
|----|--------------------------|
| Linux / macOS / Windows | `git --version` |

Si Git manque : [git-scm.com/downloads](https://git-scm.com/downloads).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 3. Créer le projet et l'initialiser avec Git

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 3.1 Créer le dossier

#### Linux / macOS (bash ou zsh)
```bash
mkdir mini-app-openclaude-python
cd mini-app-openclaude-python
```

#### Windows (PowerShell)
```powershell
mkdir mini-app-openclaude-python
cd mini-app-openclaude-python
```

> Sur Windows, `mkdir` est un alias PowerShell de `New-Item -ItemType Directory`.

### 3.2 Créer un `main.py` vide

#### Linux / macOS
```bash
touch main.py
```

#### Windows (PowerShell)
```powershell
New-Item main.py
```

> **Note** : sur Windows il n'existe pas de `touch` natif. `New-Item` est l'équivalent.

### 3.3 Initialiser Git

```bash
git init
git add main.py
git commit -m "Initialisation du projet Python"
```

Cette suite de 3 commandes est **identique sur les trois OS**.

Si Git refuse le commit en demandant votre identité :

```bash
git config --global user.name "Votre Nom"
git config --global user.email "votre.email@example.com"
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

## 4. Lancer OpenClaude dans le bon dossier

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


> **Critique** : OpenClaude ne « voit » que le dossier dans lequel il est lancé. Si vous le démarrez ailleurs, il ne trouvera pas `main.py` ni `CLAUDE.md`.

### 4.1 Vérifier où vous êtes

#### Linux / macOS
```bash
pwd
```

#### Windows (PowerShell)
```powershell
Get-Location
# Ou plus court :
pwd
```

Vous devez voir un chemin qui se termine par `mini-app-openclaude-python`.

### 4.2 Lancer OpenClaude

```bash
openclaude
```

Identique sur les trois OS. Si la commande n'est pas reconnue, fermez et rouvrez le terminal (le PATH n'a pas été rechargé).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 5. Configurer OpenClaude pour utiliser Ollama

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Il y a deux méthodes : **slash command** dans OpenClaude, ou **variables d'environnement** avant de le lancer. La seconde est plus fiable.

### 5.1 Méthode A — slash command

Dans OpenClaude, tapez :

```
/provider
```

Choisissez **Ollama** dans la liste, puis le modèle (par exemple `qwen2.5-coder:7b`).

> Si `/provider` n'existe pas dans votre version d'OpenClaude, passez à la méthode B.

### 5.2 Méthode B — variables d'environnement (recommandée)

Quittez OpenClaude (`Ctrl+C`), puis exportez les variables avant de le relancer.

#### Linux / macOS (bash ou zsh)
```bash
export CLAUDE_CODE_USE_OPENAI=1
export OPENAI_BASE_URL=http://localhost:11434/v1
export OPENAI_MODEL=qwen2.5-coder:7b
openclaude
```

#### Windows (PowerShell)
```powershell
$env:CLAUDE_CODE_USE_OPENAI="1"
$env:OPENAI_BASE_URL="http://localhost:11434/v1"
$env:OPENAI_MODEL="qwen2.5-coder:7b"
openclaude
```

#### Windows (cmd.exe — alternatif)
```cmd
set CLAUDE_CODE_USE_OPENAI=1
set OPENAI_BASE_URL=http://localhost:11434/v1
set OPENAI_MODEL=qwen2.5-coder:7b
openclaude
```

> **Différence Linux/Mac vs Windows** : sur bash/zsh on utilise `export VAR=val`, sur PowerShell `$env:VAR="val"`, sur cmd `set VAR=val`. Ces variables ne durent que le temps de la session.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 6. Découvrir les slash commands utiles

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Dans OpenClaude, tapez :

```
/help
```

puis simplement :

```
/
```

pour voir la liste des commandes disponibles dans **votre** version. Voici les plus utiles :

| Commande | Effet | Fiabilité avec Ollama |
|----------|-------|----------------------|
| `/help` | Affiche l'aide | OK |
| `/` | Liste les commandes | OK |
| `/clear` | Efface l'historique de conversation | OK |
| `/provider` | Configure le fournisseur (Ollama, etc.) | OK |
| `/init` | Génère `CLAUDE.md` automatiquement | **Peu fiable** → on l'évite |
| `/diff` | Affiche les changements | **Peu fiable** → on utilise `git diff` |
| `/plan` | Mode planification | **Peu fiable** → on rédige le prompt à la main |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 7. Pourquoi on n'utilise pas `/init`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Avec Claude Code officiel, `/init` génère un bon `CLAUDE.md` adapté au projet.

Avec OpenClaude + Ollama, `/init` produit souvent :

- un contenu trop générique (style README de produit) ;
- des sections inutiles pour un TP (« Contributing », badges, emojis) ;
- une structure orientée « projet open source » et non « TP étudiant ».

**Solution** : on écrit `CLAUDE.md` à la main. C'est plus rapide et 100 % adapté au TP.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 8. Créer `CLAUDE.md` à la main

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 8.1 Créer le fichier vide

#### Linux / macOS
```bash
touch CLAUDE.md
```

#### Windows (PowerShell)
```powershell
New-Item CLAUDE.md
```

### 8.2 Ouvrir le projet dans VS Code

```bash
code .
```

Identique sur les trois OS, à condition que la commande `code` soit dans le PATH (sur macOS, lancez VS Code une fois puis `Cmd+Shift+P` → `Shell Command: Install 'code' command in PATH`).

### 8.3 Coller le contenu suivant dans `CLAUDE.md`

> **Astuce** : ce contenu est lui-même du Markdown. Pour éviter les problèmes de blocs de code imbriqués, on le présente ici délimité par des **tildes** (`~~~`) au lieu de backticks. Ne copiez **que le contenu** entre les deux lignes de tildes.

~~~markdown
# CLAUDE.md — Instructions du projet

## 1. Contexte
Mini application Python pour étudiants débutants.
Bibliothèque graphique : Tkinter (incluse avec Python).
Fichier principal : `main.py`.

## 2. Objectifs pédagogiques
- structure d'un programme Python simple ;
- usage de Tkinter (Label, Entry, Button, Listbox) ;
- sauvegarde JSON ;
- contrôle de version avec Git.

## 3. Contraintes
- uniquement Python + Tkinter ;
- aucune dépendance externe ;
- aucun framework web (Flask, Django, FastAPI) ;
- aucun JS / React / npm pour le projet Python ;
- code lisible, commenté, pédagogique ;
- éviter les classes au début, sauf si demandé.

## 4. Structure attendue
```
mini-app-openclaude-python/
├── CLAUDE.md
├── main.py
├── tasks.json        (créé après l'étape sauvegarde)
└── procedures/       (créé après l'étape procédures)
```

## 5. Lancer l'application
- Linux / macOS : `python3 main.py`
- Windows       : `python main.py`

## 6. Règles de réponse pour l'assistant
1. Lire le fichier concerné.
2. Proposer un mini-plan avant de modifier.
3. Donner du code simple, commenté.
4. Expliquer chaque changement.
5. Donner la commande de test.
6. Mentionner les erreurs possibles.
~~~

Sauvegardez `CLAUDE.md`.

### 8.4 Committer

```bash
git add CLAUDE.md
git commit -m "Ajout des instructions projet CLAUDE.md"
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

## 9. Demander un plan à OpenClaude

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Dans la session OpenClaude, tapez :

```
Lis le fichier CLAUDE.md. Résume les règles principales en 5 points.
Ensuite, propose un plan simple pour la version 1 de l'application.

Contraintes :
- un seul fichier main.py ;
- Tkinter uniquement ;
- pas de classes ;
- commentaires pédagogiques.
```

Vous attendez une réponse du type :

```
1. Importer tkinter et messagebox.
2. Créer la fenêtre principale.
3. Ajouter un titre (Label).
4. Ajouter un champ Entry.
5. Ajouter une Listbox.
6. Créer ajouter_tache(), terminer_tache(), supprimer_tache().
7. Ajouter les boutons.
8. Lancer fenetre.mainloop().
```

> **Drapeau rouge** : si le plan mentionne React, Flask, Django, FastAPI ou une base de données, **arrêtez-le** : « Non. Respecte CLAUDE.md. Python + Tkinter uniquement. »

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 10. Demander la version 1 du code et la coller dans `main.py`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 10.1 Demander le code

Dans OpenClaude :

```
Génère maintenant le code de la version 1 dans un seul bloc Python.
Je vais le copier-coller moi-même dans main.py.

Contraintes :
- Tkinter uniquement ;
- une fenêtre, un Label titre, un Entry, une Listbox ;
- trois boutons : Ajouter, Marquer comme terminée, Supprimer ;
- commentaires pédagogiques ;
- pas de classes.
```

### 10.2 Copier-coller dans `main.py`

OpenClaude renvoie un bloc de code. **Sélectionnez tout le bloc**, copiez, puis ouvrez `main.py` dans VS Code et collez. Sauvegardez (`Ctrl+S` / `Cmd+S`).

Le code attendu ressemble à ceci (à titre de référence) :

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

## 11. Comprendre le code généré

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 11.1 Imports

`import tkinter as tk` charge la bibliothèque graphique standard et lui donne l'alias court `tk`.
`from tkinter import messagebox` permet d'afficher des boîtes de dialogue (`showwarning`, `askyesno`).

### 11.2 Les trois fonctions

| Fonction | Rôle | Astuce |
|----------|------|--------|
| `ajouter_tache()` | Lit `entree_tache.get()`, refuse si vide (`.strip() == ""`), insère dans la `Listbox`, vide le champ. | `tk.END` = « à la fin ». |
| `terminer_tache()` | Lit la sélection (`curselection()`), préfixe le texte avec `"[Terminée] "`. | On évite le double préfixe via `startswith`. |
| `supprimer_tache()` | Supprime l'élément à l'index sélectionné. | Refuse si rien n'est sélectionné. |

### 11.3 La fenêtre et les widgets

- `tk.Tk()` crée la fenêtre principale.
- `.title()` et `.geometry("500x400")` la nomment et la dimensionnent.
- Les widgets `Label`, `Entry`, `Button`, `Listbox` sont placés avec `.pack(pady=...)`.

### 11.4 La boucle d'événements

`fenetre.mainloop()` est **obligatoire** : sans elle, la fenêtre s'ouvre puis se ferme aussitôt.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 12. Tester et committer la version 1

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 12.1 Lancer

#### Linux / macOS
```bash
python3 main.py
```

#### Windows (PowerShell)
```powershell
python main.py
```

Une fenêtre s'ouvre. Testez :

```
1. Tapez "Acheter du lait", cliquez Ajouter.
2. Sélectionnez la tâche, cliquez "Marquer comme terminée".
3. Sélectionnez à nouveau, cliquez "Supprimer".
```

### 12.2 Vérifier les changements avec Git

```bash
git diff
```

Identique sur les trois OS. Lisez attentivement ce que vous avez collé.

### 12.3 Committer

```bash
git add main.py
git commit -m "Création de la version 1 (Tkinter)"
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

## 13. Amélioration : bouton « Effacer toutes les tâches »

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 13.1 Demander un mini-plan

Dans OpenClaude :

```
Mini-plan SVP : ajouter un bouton "Effacer toutes les tâches"
qui demande confirmation via messagebox.askyesno avant de tout effacer.
Réponds en 4 étapes maximum.
```

### 13.2 Demander le code, copier-coller

```
OK, donne-moi le code à insérer dans main.py.
Indique précisément : "ajoute cette fonction ici" et "ajoute ce bouton ici".
Je vais éditer main.py à la main.
```

Code typique à coller (fonction + bouton) :

```python
def effacer_toutes_les_taches():
    confirmation = messagebox.askyesno(
        "Confirmation",
        "Voulez-vous vraiment effacer toutes les tâches ?"
    )
    if confirmation:
        liste_taches.delete(0, tk.END)


tk.Button(fenetre, text="Effacer toutes les tâches", width=25,
          command=effacer_toutes_les_taches).pack(pady=5)
```

### 13.3 Tester puis committer

```bash
python3 main.py     # ou python main.py sous Windows
git diff
git add main.py
git commit -m "Ajout du bouton Effacer toutes les tâches"
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

## 14. Amélioration : sauvegarde JSON persistante

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 14.1 Objectif

Quand on ferme l'application et qu'on la relance, les tâches sont **toujours là**, sauvegardées dans `tasks.json`.

### 14.2 Demander le code à OpenClaude

```
Modifie main.py pour ajouter une sauvegarde JSON.
Je vais copier-coller moi-même.

Règles :
- module standard json uniquement ;
- fichier tasks.json à côté de main.py ;
- sauvegarder après ajout, suppression, marquage terminé, effacement total ;
- charger au démarrage, juste après la création de la Listbox ;
- pas de classes ;
- commentaires pédagogiques.

Donne-moi le main.py complet final dans un seul bloc.
```

### 14.3 Code complet attendu

```python
import json
import tkinter as tk
from tkinter import messagebox

FICHIER_TACHES = "tasks.json"


def sauvegarder_taches():
    taches = list(liste_taches.get(0, tk.END))
    with open(FICHIER_TACHES, "w", encoding="utf-8") as f:
        json.dump(taches, f, ensure_ascii=False, indent=4)


def charger_taches():
    try:
        with open(FICHIER_TACHES, "r", encoding="utf-8") as f:
            for tache in json.load(f):
                liste_taches.insert(tk.END, tache)
    except FileNotFoundError:
        pass


def ajouter_tache():
    texte = entree_tache.get()
    if texte.strip() == "":
        messagebox.showwarning("Attention", "Veuillez écrire une tâche.")
        return
    liste_taches.insert(tk.END, texte)
    entree_tache.delete(0, tk.END)
    sauvegarder_taches()


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
    sauvegarder_taches()


def supprimer_tache():
    selection = liste_taches.curselection()
    if not selection:
        messagebox.showwarning("Attention", "Veuillez sélectionner une tâche.")
        return
    liste_taches.delete(selection[0])
    sauvegarder_taches()


def effacer_toutes_les_taches():
    if messagebox.askyesno("Confirmation",
                           "Voulez-vous vraiment effacer toutes les tâches ?"):
        liste_taches.delete(0, tk.END)
        sauvegarder_taches()


fenetre = tk.Tk()
fenetre.title("Mini Gestionnaire de Tâches")
fenetre.geometry("500x450")

tk.Label(fenetre, text="Gestionnaire de tâches",
         font=("Arial", 18, "bold")).pack(pady=10)

entree_tache = tk.Entry(fenetre, width=40, font=("Arial", 12))
entree_tache.pack(pady=10)

tk.Button(fenetre, text="Ajouter", width=25,
          command=ajouter_tache).pack(pady=5)

liste_taches = tk.Listbox(fenetre, width=50, height=10, font=("Arial", 12))
liste_taches.pack(pady=10)

tk.Button(fenetre, text="Marquer comme terminée", width=25,
          command=terminer_tache).pack(pady=5)
tk.Button(fenetre, text="Supprimer", width=25,
          command=supprimer_tache).pack(pady=5)
tk.Button(fenetre, text="Effacer toutes les tâches", width=25,
          command=effacer_toutes_les_taches).pack(pady=5)

charger_taches()
fenetre.mainloop()
```

### 14.4 Tester la persistance

1. Lancez l'app, ajoutez 3 tâches, fermez la fenêtre.
2. Vérifiez que `tasks.json` existe à côté de `main.py`.
3. Relancez : les 3 tâches doivent réapparaître.

### 14.5 Ignorer `tasks.json` dans Git

Créez un `.gitignore` :

#### Linux / macOS
```bash
touch .gitignore
```

#### Windows (PowerShell)
```powershell
New-Item .gitignore
```

Contenu :

```
tasks.json
__pycache__/
*.pyc
```

Puis :

```bash
git add .gitignore main.py
git commit -m "Ajout sauvegarde JSON + .gitignore"
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

## 15. Procédures réutilisables (équivalent local des skills)

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 15.1 Pourquoi ?

Avec **Claude Code officiel**, on peut créer une skill `~/.claude/skills/python-feature/SKILL.md` et l'invoquer par `/python-feature`. C'est automatique.

Avec **OpenClaude**, ces skills ne sont **pas chargées**. On doit donc :

1. créer un fichier `procedures/python-feature.md` dans le projet ;
2. demander explicitement à OpenClaude de le lire avant chaque action.

### 15.2 Créer le dossier `procedures/`

#### Linux / macOS
```bash
mkdir procedures
touch procedures/python-feature.md
touch procedures/explain-python.md
touch procedures/debug-python.md
```

#### Windows (PowerShell)
```powershell
New-Item -ItemType Directory procedures
New-Item procedures\python-feature.md
New-Item procedures\explain-python.md
New-Item procedures\debug-python.md
```

### 15.3 Contenu de `procedures/python-feature.md`

```markdown
# Procédure — Ajouter une fonctionnalité Python Tkinter

1. Lire CLAUDE.md.
2. Lire main.py.
3. Proposer un mini-plan en 3-5 étapes.
4. Donner le code à insérer (avec emplacement précis).
5. Donner la commande de test.
6. Pas de dépendance externe.
7. Pas de classes au début.
8. Commentaires pédagogiques obligatoires.
```

### 15.4 Utiliser la procédure

Dans OpenClaude :

```
Lis procedures/python-feature.md et applique cette procédure.

Fonctionnalité à ajouter : un bouton "Compter" qui affiche dans une messagebox
le nombre total de tâches dans la Listbox.
```

OpenClaude vous renvoie le plan + le code, vous copiez-collez.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 16. Tableau comparatif final

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Besoin | Claude Code officiel | OpenClaude + Ollama (ce TP) |
|--------|---------------------|------------------------------|
| Lancer | `claude` | `openclaude` |
| Modèle | Claude (cloud) | Qwen / Gemma / Llama (local) |
| Coût | abonnement | gratuit |
| Mode opératoire | agent (modifie les fichiers) | chat (vous copiez-collez) |
| Initialiser | `/init` | `CLAUDE.md` à la main |
| Voir l'aide | `/help` | `/help` |
| Choisir le fournisseur | non nécessaire | `/provider` ou variables d'env |
| Voir les modifs | `/diff` | `git diff` |
| Commandes personnalisées | skills `~/.claude/skills/` | `procedures/*.md` lus à la demande |
| Fiabilité du tool-calling | très haute | basse → mode chat obligatoire |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 17. À retenir

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


> Un assistant de code **ne remplace pas** la méthode. Il **amplifie** une bonne méthode.

La bonne méthode reste la même quel que soit l'outil :

```
Contexte (CLAUDE.md)
  → Plan demandé explicitement
  → Petite modification générée
  → Lecture + git diff
  → Test
  → Commit
```

Avec OpenClaude + Ollama, **vous gardez le contrôle** parce que c'est vous qui collez chaque ligne dans `main.py`. C'est plus lent qu'un agent, mais c'est aussi un excellent exercice pédagogique : vous **lisez** vraiment ce que l'IA produit.

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


### Erreurs Python / Tkinter

| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| `ModuleNotFoundError: No module named 'tkinter'` | Tkinter pas installé (Linux) | `sudo apt install python3-tk` (Debian/Ubuntu), `sudo dnf install python3-tkinter` (Fedora), `brew install python-tk` (Mac) |
| `python: command not found` (Linux/Mac) | Binaire s'appelle `python3` | Utilisez `python3 main.py` |
| `python` ouvre le Microsoft Store (Windows) | Alias système non remplacé | Réinstaller Python depuis [python.org](https://www.python.org), cocher *Add to PATH* |
| `NameError: name 'liste_taches' is not defined` | `charger_taches()` appelé avant la création de la Listbox | Mettre `charger_taches()` **après** `liste_taches = tk.Listbox(...)` |
| Fenêtre se ferme aussitôt | Oubli de `fenetre.mainloop()` | Ajouter la ligne en bas du fichier |

### Erreurs OpenClaude / Ollama

| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| `openclaude: command not found` | PATH npm pas rechargé | Fermer/rouvrir le terminal ; sinon réinstaller : `npm install -g @gitlawb/openclaude` |
| `ECONNREFUSED 11434` | Serveur Ollama pas lancé | `ollama serve` (Linux/Mac) ou icône Ollama dans la barre des tâches (Windows) |
| OpenClaude répond mais ignore `CLAUDE.md` | Mémoire pas chargée auto | Demander explicitement : `Lis CLAUDE.md et résume.` |
| OpenClaude propose React / Flask | Modèle a oublié les contraintes | Répondre : `Non. Respecte CLAUDE.md. Python + Tkinter uniquement.` |
| Réponse très lente | Pas de GPU détecté | `ollama ps` pour vérifier ; sinon utiliser un modèle plus petit (`gemma2:2b`) |
| `/diff` ne fait rien | Slash command non implémentée | Utiliser `git diff` à la place |

### Erreurs Git

| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| `Author identity unknown` | `user.name` / `user.email` pas configurés | `git config --global user.name "..."` puis `user.email "..."` |
| `nothing to commit` après modification | Vous avez oublié `git add` | `git add main.py` puis `git commit -m "..."` |
| Conflit d'encodage Windows (accents cassés) | Console en CP1252 | `chcp 65001` avant de relancer Python |

### Erreurs de structure

| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| OpenClaude ne voit pas `main.py` | Lancé dans le mauvais dossier | `cd` dans `mini-app-openclaude-python` puis relancer `openclaude` |
| `main.py` reste vide après une demande | OpenClaude est un **chat**, pas un agent | Copier-coller manuellement le code donné en réponse |
| Code généré ne suit pas les contraintes | `CLAUDE.md` jamais lu | Demander explicitement : `Lis CLAUDE.md avant de répondre.` |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>
