# 06 — TP mini-app Python/Tkinter sur **macOS** : OpenClaude + Ollama (mode chat)

> **Public** : étudiants débutants en Python.
> **Coût** : 100 % gratuit (Ollama local).
> **OS** : macOS (Apple Silicon ou Intel).
> **Durée** : 1 h à 1 h 30.
> **Pré-requis** :
> - [Tuto 01 — Ollama installé](./01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md)
> - [Tuto 02 — OpenClaude installé et branché sur Ollama](./02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md)
> - Python 3.9+ avec Tkinter
> - Un terminal et un éditeur (VS Code, nano, etc.)

> Pour Linux → [`../linux/06-[OPENCLAUDE-GRATUIT]-...`](../linux/06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md).
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

```bash
python3 --version
python3 -c "import tkinter; print('Tkinter OK')"
```

Si Python n'est pas installé :
```bash
brew install python-tk
```

> Sur macOS, le `python3` système n'inclut pas toujours Tkinter (selon la version macOS). `brew install python-tk` installe les deux.

### Vérifier Ollama

```bash
ollama list
curl http://127.0.0.1:11434/api/tags
```

Si pas de réponse : `brew services restart ollama` ou relance Ollama.app.

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

[↑ Sommaire](#sommaire)

---

## 3. Créer le dossier de projet

```bash
mkdir -p ~/projets/gestionnaire-taches
cd ~/projets/gestionnaire-taches
```

[↑ Sommaire](#sommaire)

---

## 4. Lancer OpenClaude

Dans le dossier du projet :

```bash
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

Dans un autre terminal :

```bash
nano main.py
```

Ou ouvre VS Code dans le dossier :
```bash
code .
```

Colle le code reçu d'OpenClaude. Sauvegarde.

### Lance

```bash
python3 main.py
```

Si une fenêtre s'ouvre → ✅ Étape A validée.

> Sur macOS, le **Dock** peut afficher temporairement une icône Python. Normal.

### Si erreur

Copie le message d'erreur dans OpenClaude :

```
J'ai cette erreur quand je lance python3 main.py :
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

Remplace dans `main.py`.

### Test

```bash
python3 main.py
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

```bash
cat tasks.json
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

```bash
cd ~/projets/gestionnaire-taches
git init -b main

cat > .gitignore <<'EOF'
__pycache__/
*.pyc
.venv/
.env
.DS_Store
EOF

git add .
git status
git commit -m "TP mini-app : gestionnaire de tâches Tkinter avec persistance JSON"
git log --oneline
```

> Le `.DS_Store` est un fichier de métadonnées créé par le Finder macOS — toujours l'ignorer.

[↑ Sommaire](#sommaire)

---

## Troubleshooting

| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| `ModuleNotFoundError: No module named 'tkinter'` | Tkinter pas installé | `brew install python-tk` |
| Fenêtre Tkinter floue sur Retina | DPI scaling | Ajoute `app.tk.call('tk', 'scaling', 2.0)` au début |
| OpenClaude génère du code Python 2 | Modèle ancien | Précise : *« en Python 3.9+ »* |
| OpenClaude réécrit tout le fichier à chaque demande | Pas de contraintes dans le prompt | Demande : *« seulement la méthode X »* |
| Le code ne marche pas du premier coup | Hallucination LLM | Copie l'erreur, demande un correctif |
| `python` au lieu de `python3` ne fonctionne pas | macOS récent n'a plus python = python3 | Toujours `python3` |
| `tasks.json` corrompu | Crash pendant sauvegarde | `rm tasks.json` puis relance |

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

- [`07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md`](./07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md) — refais le TP avec OpenRouter cloud (modèle plus puissant, sans pression sur ta RAM unifiée)
- [`08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md`](./08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md) — refais le TP avec Claude Code officiel en mode agent

[↑ Sommaire](#sommaire)
