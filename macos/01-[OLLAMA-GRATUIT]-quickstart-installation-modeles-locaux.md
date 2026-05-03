# 01 — Ollama (gratuit) sur **macOS** : installation et modèles locaux

> **Public** : tous niveaux.
> **Coût** : gratuit (modèles tournent en local).
> **OS** : macOS (Apple Silicon M1/M2/M3 ou Intel).
> **Durée** : 15 à 30 min.
> **Pré-requis** : 8 Go de RAM minimum, Apple Silicon recommandé pour la performance (Metal).

> Pour Linux → [`../linux/01-[OLLAMA-GRATUIT]-...`](../linux/01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md).
> Pour Windows → [`../windows/01-[OLLAMA-GRATUIT]-...`](../windows/01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md).

---

## Sommaire

1. [Étape 1 — Installer Ollama](#étape-1--installer-ollama)
2. [Étape 2 — Vérifier que ça marche](#étape-2--vérifier-que-ça-marche)
3. [Étape 3 — Vérifier que le serveur tourne](#étape-3--vérifier-que-le-serveur-tourne)
4. [Étape 4 — Méthode A : modèle de la bibliothèque Ollama](#étape-4--méthode-a--modèle-de-la-bibliothèque-ollama)
5. [Étape 5 — Méthode B : modèle custom depuis Hugging Face](#étape-5--méthode-b--modèle-custom-depuis-hugging-face)
6. [Étape 6 — Modelfile avec paramètres (optionnel)](#étape-6--modelfile-avec-paramètres-optionnel)
7. [Étape 7 — Cheatsheet des commandes utiles](#étape-7--cheatsheet-des-commandes-utiles)
8. [Étape 8 — Dans le chat Ollama (commandes slash)](#étape-8--dans-le-chat-ollama-commandes-slash)
9. [Étape 9 — Tester l'API depuis un autre programme](#étape-9--tester-lapi-depuis-un-autre-programme)
10. [Récap : le flux complet en une page](#récap--le-flux-complet-en-une-page)
11. [Troubleshooting](#troubleshooting)
12. [Suite](#suite)

---

## Étape 1 — Installer Ollama

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Deux méthodes, choisis-en une.

### Méthode A — Homebrew (recommandée)

Si tu n'as pas Homebrew : [brew.sh](https://brew.sh).

```bash
brew install ollama
brew services start ollama
```

`brew services` enregistre Ollama comme service launchd (démarrage auto).

### Méthode B — Installeur officiel

1. Télécharge [ollama.com/download/mac](https://ollama.com/download/mac)
2. Ouvre le `.dmg`, glisse `Ollama.app` dans `Applications`
3. Lance Ollama une fois depuis Launchpad — il tourne en arrière-plan (icône llama dans la barre du haut)

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 2 — Vérifier que ça marche

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
ollama --version
```

Tu dois voir `ollama version is 0.x.x`. Si non → ferme et rouvre ton terminal pour recharger le PATH (zsh recharge `~/.zshrc`).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 3 — Vérifier que le serveur tourne

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Ollama lance un serveur HTTP sur `127.0.0.1:11434` automatiquement.

```bash
curl http://127.0.0.1:11434/api/tags
```

Si ça répond avec un JSON → OK. Si non :

```bash
# Méthode Homebrew
brew services restart ollama

# Méthode .app
# Quitte et relance Ollama.app depuis le menu Apple > Forcer à quitter

# Lancement manuel
ollama serve
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

## Étape 4 — Méthode A : modèle de la bibliothèque Ollama

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Pour tous les modèles listés sur [ollama.com/library](https://ollama.com/library).

### Télécharger

```bash
ollama pull qwen2.5:7b
```

### Lancer

```bash
ollama run qwen2.5:7b
```

Tape ta question. Sors avec `/bye`.

### Modèles recommandés selon ta machine

| RAM unifiée Apple Silicon | Modèle | Commande |
|---------------------------|--------|----------|
| 8 Go (M1/M2 base) | gemma2:2b ou qwen2.5:3b | `ollama pull gemma2:2b` |
| 16 Go | qwen2.5:7b | `ollama pull qwen2.5:7b` |
| 16 Go (code) | qwen2.5-coder:7b | `ollama pull qwen2.5-coder:7b` |
| 24-32 Go | qwen2.5:14b | `ollama pull qwen2.5:14b` |
| 64 Go+ (M3 Max) | qwen2.5:32b | `ollama pull qwen2.5:32b` |

> Sur Apple Silicon, la « RAM unifiée » fait office de VRAM. Pas besoin de carte graphique séparée — Metal fait l'accélération.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 5 — Méthode B : modèle custom depuis Hugging Face

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Pour les modèles qui **ne sont pas** dans la bibliothèque Ollama.

### 5.1 Télécharger le `.gguf` depuis HuggingFace

Va sur la page du modèle, onglet **Files and versions**, télécharge un `.gguf` selon ta RAM unifiée :

- 8 Go → `*-Q4_K_M.gguf`
- 16 Go → `*-Q6_K_P.gguf`
- 24 Go+ → `*-Q8_0.gguf`

Place le fichier dans un dossier dédié :

```bash
mkdir -p ~/models/gemma-local
mv ~/Downloads/Gemma-4-E4B-Uncensored-Q6_K_P.gguf ~/models/gemma-local/
cd ~/models/gemma-local
```

### 5.2 Créer le Modelfile

Dans le **même dossier** que le `.gguf`, crée un fichier `Modelfile` (sans extension) :

```bash
echo "FROM ./Gemma-4-E4B-Uncensored-Q6_K_P.gguf" > Modelfile
```

### 5.3 Vérifier

```bash
cat Modelfile
ls *.gguf
```

### 5.4 Enregistrer le modèle dans Ollama

```bash
ollama create gemma-local -f Modelfile
```

Attends `success` (30 s à 2 min).

### 5.5 Lancer

```bash
ollama run gemma-local
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

## Étape 6 — Modelfile avec paramètres (optionnel)

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```
FROM ./Gemma-4-E4B-Uncensored-Q6_K_P.gguf

PARAMETER temperature 0.7
PARAMETER num_ctx 8192
PARAMETER repeat_penalty 1.1

SYSTEM "Tu réponds toujours en français."
```

Puis :

```bash
ollama create gemma-local -f Modelfile
```

(Refais `create` à chaque modif du Modelfile.)

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 7 — Cheatsheet des commandes utiles

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Action | Commande |
|--------|----------|
| Lister les modèles installés | `ollama list` |
| Voir les infos d'un modèle | `ollama show gemma-local` |
| Voir le Modelfile actuel | `ollama show gemma-local --modelfile` |
| Voir les paramètres | `ollama show gemma-local --parameters` |
| Copier/aliaser | `ollama cp gemma-local gemma-test` |
| Supprimer | `ollama rm gemma-test` |
| Stopper en mémoire | `ollama stop gemma-local` |
| Voir ce qui tourne | `ollama ps` |
| Tuer Ollama | `pkill ollama` |
| Redémarrer (Homebrew) | `brew services restart ollama` |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 8 — Dans le chat Ollama (commandes slash)

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Commande | Effet |
|----------|-------|
| `/?` | Aide |
| `/set system "..."` | Changer le prompt système |
| `/set parameter temperature 0.5` | Changer un paramètre |
| `/show info` | Infos du modèle |
| `/clear` | Effacer la conversation |
| `/bye` | Sortir |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 9 — Tester l'API depuis un autre programme

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
curl http://127.0.0.1:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"gemma-local","messages":[{"role":"user","content":"dis bonjour"}]}'
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

## Récap : le flux complet en une page

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```
1. brew install ollama && brew services start ollama
2. curl 127.0.0.1:11434/api/tags

Modèle standard (lib Ollama) :
3a. ollama pull qwen2.5:7b
3b. ollama run qwen2.5:7b

Modèle custom (HuggingFace) :
3a. mkdir ~/models/<nom> && cd ~/models/<nom>
3b. mv ~/Downloads/<modele>.gguf .
3c. echo "FROM ./<modele>.gguf" > Modelfile
3d. ollama create mon-modele -f Modelfile
3e. ollama run mon-modele
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


| Erreur | Cause | Fix |
|--------|-------|-----|
| `Error: pull model manifest: file does not exist` | Tu pulls un modèle HuggingFace custom | Va à l'**étape 5** (Modelfile + create) |
| `'FROM' n'est pas reconnu` | Tu tapes `FROM ./...` dans le terminal | C'est la syntaxe **dans Modelfile**, pas dans le shell |
| `ECONNREFUSED 11434` | Service Ollama pas lancé | `brew services restart ollama` ou relance Ollama.app |
| `command not found: ollama` | PATH pas rechargé | Ferme/rouvre le terminal, ou `source ~/.zshrc` |
| Notepad-style `Modelfile.txt` | Macros TextEdit | Utilise `echo "..." > Modelfile` au lieu de TextEdit |
| Modèle très lent (Intel Mac) | Pas de GPU détecté | Sur Mac Intel, Ollama utilise le CPU. Prends un modèle plus petit (`gemma2:2b`) |
| Mémoire saturée | RAM unifiée pleine | Ferme d'autres apps, ou prends une quantization plus légère (Q4 au lieu de Q8) |

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


- [`02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md`](./02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md) — installer OpenClaude et le brancher sur Ollama
- [`03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md`](./03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md) — alternative cloud gratuite (sans GPU)
- [`04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md`](./04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md) — alternative avancée à Ollama
- [Bibliothèque officielle Ollama](https://ollama.com/library)

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>
