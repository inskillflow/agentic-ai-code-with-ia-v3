# 02 — Ollama (gratuit) : installation et modèles locaux, étape par étape

> **Public** : tous niveaux.
> **Coût** : gratuit (modèles tournent en local).
> **OS supportés** : Linux, macOS, Windows.
> **Durée** : 15 à 30 min.
> **Pré-requis** : au moins 4 Go de RAM/VRAM disponible.

Guide concis : pas de blabla, tu fais ça, ça, ça.

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
12. [Pour aller plus loin](#pour-aller-plus-loin)

---

## Étape 1 — Installer Ollama

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Windows

Télécharge → installe → lance.

```
https://ollama.com/download/windows
```

### macOS

```bash
brew install ollama
```

### Linux

```bash
curl -fsSL https://ollama.com/install.sh | sh
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

## Étape 2 — Vérifier que ça marche

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
ollama --version
```

Tu dois voir `ollama version is 0.x.x`. Si non → relance ton terminal.

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
ollama serve
```

(Laisse ce terminal ouvert ou utilise un second terminal pour la suite.)

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

| RAM/VRAM | Modèle | Commande |
|----------|--------|----------|
| 4 Go | gemma2:2b | `ollama pull gemma2:2b` |
| 8 Go | qwen2.5:7b | `ollama pull qwen2.5:7b` |
| 8 Go (code) | qwen2.5-coder:7b | `ollama pull qwen2.5-coder:7b` |
| 16 Go | qwen2.5:14b | `ollama pull qwen2.5:14b` |

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


Pour les modèles qui **ne sont pas** dans la bibliothèque Ollama (uncensored, finetunes, modèles exotiques...).

### 5.1 — Télécharger le `.gguf` depuis HuggingFace

Va sur la page du modèle, onglet **Files and versions**, télécharge un `.gguf` selon ta VRAM :

- 4 Go → `*-Q4_K_M.gguf`
- 8 Go → `*-Q6_K_P.gguf`
- 12 Go+ → `*-Q8_0.gguf`

Place le fichier dans un dossier, exemple :

```
C:\Users\rehou\Desktop\gemma-local\Gemma-4-E4B-Uncensored-Q6_K_P.gguf
```

### 5.2 — Créer le Modelfile

Dans le **même dossier** que le `.gguf`, crée un fichier nommé `Modelfile` (sans extension).

#### Méthode rapide (PowerShell, sans piège)

```powershell
cd C:\Users\rehou\Desktop\gemma-local
Set-Content -Path "Modelfile" -Value "FROM ./Gemma-4-E4B-Uncensored-Q6_K_P.gguf" -NoNewline -Encoding ASCII
```

#### Méthode rapide (Mac/Linux)

```bash
cd ~/gemma-local
echo "FROM ./Gemma-4-E4B-Uncensored-Q6_K_P.gguf" > Modelfile
```

⚠️ **Piège** : si tu utilises Notepad, choisis « Tous les fichiers » dans le dialogue Enregistrer, sinon Windows met `Modelfile.txt`.

### 5.3 — Vérifier

```bash
# Windows
type Modelfile
dir *.gguf

# Mac/Linux
cat Modelfile
ls *.gguf
```

Tu dois voir le fichier `Modelfile` (sans extension) et le `.gguf`.

### 5.4 — Enregistrer le modèle dans Ollama

```bash
ollama create gemma-local -f Modelfile
```

Attends `success` (30 s à 2 min selon la taille).

### 5.5 — Lancer

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


Si tu veux personnaliser temperature, contexte, prompt système, remplace le contenu de `Modelfile` par :

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
| Copier/aliaser un modèle | `ollama cp gemma-local gemma-test` |
| Supprimer un modèle | `ollama rm gemma-test` |
| Stopper un modèle en mémoire | `ollama stop gemma-local` |
| Voir ce qui tourne en VRAM | `ollama ps` |
| Tuer Ollama (Windows) | `Get-Process ollama* \| Stop-Process -Force` |
| Tuer Ollama (Mac/Linux) | `pkill ollama` |

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


Une fois `ollama run gemma-local` lancé :

| Commande | Effet |
|----------|-------|
| `/?` | Aide |
| `/set system "..."` | Changer le prompt système à la volée |
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


Ollama expose une API compatible OpenAI sur `http://127.0.0.1:11434/v1`.

### Test PowerShell

```powershell
$body = @{
    model = "gemma-local"
    messages = @(@{ role = "user"; content = "dis bonjour" })
} | ConvertTo-Json

Invoke-RestMethod -Uri "http://127.0.0.1:11434/v1/chat/completions" `
    -Method Post -ContentType "application/json" -Body $body
```

### Test bash (Mac/Linux)

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
1. Installer Ollama          → ollama --version
2. Vérifier le serveur       → curl 127.0.0.1:11434/api/tags

Modèle standard (lib Ollama) :
3a. ollama pull qwen2.5:7b
3b. ollama run qwen2.5:7b

Modèle custom (HuggingFace) :
3a. Télécharger le .gguf
3b. Créer Modelfile : FROM ./mon-modele.gguf
3c. ollama create mon-modele -f Modelfile
3d. ollama run mon-modele
```

C'est tout.

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
| `'FROM' n'est pas reconnu` | Tu tapes `FROM ./...` dans le terminal | C'est la syntaxe **dans Modelfile**, pas une commande shell |
| `Modelfile.txt` au lieu de `Modelfile` | Notepad a ajouté `.txt` | `ren Modelfile.txt Modelfile` |
| `file does not exist` à `ollama create` | Mauvais nom dans le Modelfile | `dir *.gguf` puis corrige le `FROM` |
| `ECONNREFUSED 11434` | Ollama pas lancé | `ollama serve` |
| `unknown GGUF file format` | Téléchargement corrompu | Re-télécharge le `.gguf` |
| Réponse très lente | Pas de GPU détecté | `nvidia-smi` doit montrer Ollama. Sinon réinstalle Ollama après le driver NVIDIA |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Pour aller plus loin

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


- [`01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md`](./01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md) — connecter OpenClaude à Ollama
- [`03-[LLAMACPP-GRATUIT]-compilation-gguf-multiplatforme-avance.md`](./03-[LLAMACPP-GRATUIT]-compilation-gguf-multiplatforme-avance.md) — alternative avancée : compiler `llama.cpp` à la main
- [`06-[OPENCLAUDE-GRATUIT]-mini-app-gestionnaire-taches-python-tkinter-mode-chat.md`](./06-[OPENCLAUDE-GRATUIT]-mini-app-gestionnaire-taches-python-tkinter-mode-chat.md) — TP gratuit qui utilise Ollama
- [Bibliothèque officielle Ollama](https://ollama.com/library)

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>
