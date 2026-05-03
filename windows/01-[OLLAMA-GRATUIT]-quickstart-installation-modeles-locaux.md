# 01 — Ollama (gratuit) sur **Windows** : installation et modèles locaux

> **Public** : tous niveaux.
> **Coût** : gratuit (modèles tournent en local).
> **OS** : Windows 10 / 11.
> **Durée** : 15 à 30 min.
> **Pré-requis** : 8 Go de RAM minimum, GPU NVIDIA recommandé pour la performance (CUDA).
> **Terminal** : PowerShell 7 recommandé (`winget install Microsoft.PowerShell`).

> Pour Linux → [`../linux/01-[OLLAMA-GRATUIT]-...`](../linux/01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md).
> Pour macOS → [`../macos/01-[OLLAMA-GRATUIT]-...`](../macos/01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md).

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

### Méthode A — Installeur officiel (le plus simple)

1. Va sur [ollama.com/download](https://ollama.com/download).
2. Clique **Download for Windows** → tu obtiens `OllamaSetup.exe`.
3. Lance l'`exe`, accepte les défauts.
4. Ollama démarre automatiquement en arrière-plan (icône llama dans la barre système).

### Méthode B — winget

```powershell
winget install Ollama.Ollama
```

Puis lance Ollama depuis le menu Démarrer.

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


Dans **PowerShell** :

```powershell
ollama --version
```

Tu dois voir `ollama version is 0.x.x`. Si non → ferme et rouvre PowerShell pour recharger le PATH.

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


Ollama lance un serveur HTTP sur `127.0.0.1:11434` automatiquement quand l'app est ouverte.

```powershell
curl.exe http://127.0.0.1:11434/api/tags
```

> **Important** : utilise `curl.exe` (pas `curl` tout court). Sur PowerShell, `curl` est un alias de `Invoke-WebRequest` qui se comporte différemment.

Si ça répond avec un JSON → OK. Si non :

```powershell
# Vérifie que le processus tourne
Get-Process ollama* -ErrorAction SilentlyContinue

# Si rien, relance l'app depuis le menu Démarrer
# Ou en ligne de commande :
ollama serve
```

> **Piège Windows fréquent** : utilise `127.0.0.1` et **pas** `localhost`. Sur Windows, `localhost` peut résoudre vers `::1` (IPv6) avant `127.0.0.1` (IPv4), et Ollama n'écoute que sur IPv4.

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

```powershell
ollama pull qwen2.5:7b
```

### Lancer

```powershell
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


Pour les modèles qui **ne sont pas** dans la bibliothèque Ollama (uncensored, finetunes).

### 5.1 Télécharger le `.gguf` depuis HuggingFace

Va sur la page du modèle, onglet **Files and versions**, télécharge un `.gguf` selon ta VRAM :

- 4 Go → `*-Q4_K_M.gguf`
- 8 Go → `*-Q6_K_P.gguf`
- 12 Go+ → `*-Q8_0.gguf`

Place le fichier dans un dossier dédié, par exemple :

```
C:\Users\rehou\Desktop\gemma-local\Gemma-4-E4B-Uncensored-Q6_K_P.gguf
```

### 5.2 Créer le Modelfile

Dans le **même dossier** que le `.gguf`, crée un fichier `Modelfile` (sans extension).

#### Méthode rapide PowerShell (sans piège)

```powershell
cd C:\Users\rehou\Desktop\gemma-local
Set-Content -Path "Modelfile" -Value "FROM ./Gemma-4-E4B-Uncensored-Q6_K_P.gguf" -NoNewline -Encoding ASCII
```

> **Piège Notepad** : si tu utilises Notepad pour créer `Modelfile`, choisis « **Tous les fichiers** » dans le dialogue Enregistrer, sinon Windows met `Modelfile.txt`.

### 5.3 Vérifier

```powershell
type Modelfile
dir *.gguf
```

Tu dois voir le `Modelfile` (sans extension `.txt`) et le `.gguf`.

### 5.4 Enregistrer le modèle dans Ollama

```powershell
ollama create gemma-local -f Modelfile
```

Attends `success` (30 s à 2 min selon la taille).

### 5.5 Lancer

```powershell
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

```powershell
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
| Stopper en mémoire | `ollama stop gemma-local` |
| Voir ce qui tourne en VRAM | `ollama ps` |
| Tuer Ollama | `Get-Process ollama* \| Stop-Process -Force` |
| Renommer Modelfile.txt | `Rename-Item Modelfile.txt Modelfile` |

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


```powershell
$body = @{
    model = "gemma-local"
    messages = @(@{ role = "user"; content = "dis bonjour" })
} | ConvertTo-Json

Invoke-RestMethod -Uri "http://127.0.0.1:11434/v1/chat/completions" `
    -Method Post -ContentType "application/json" -Body $body
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
1. winget install Ollama.Ollama  (ou installeur .exe)
2. ollama --version
3. curl.exe 127.0.0.1:11434/api/tags

Modèle standard (lib Ollama) :
4a. ollama pull qwen2.5:7b
4b. ollama run qwen2.5:7b

Modèle custom (HuggingFace) :
4a. Télécharger .gguf dans C:\Users\<toi>\Desktop\<dossier>\
4b. Set-Content Modelfile "FROM ./mon-modele.gguf"
4c. ollama create mon-modele -f Modelfile
4d. ollama run mon-modele
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
| `'FROM' n'est pas reconnu` | Tu tapes `FROM ./...` dans PowerShell | C'est la syntaxe **dans Modelfile**, pas dans le shell |
| `Modelfile.txt` au lieu de `Modelfile` | Notepad a ajouté `.txt` automatiquement | `Rename-Item Modelfile.txt Modelfile` |
| `file does not exist` à `ollama create` | Mauvais nom dans le Modelfile | `dir *.gguf` puis corrige le `FROM` |
| `ECONNREFUSED 11434` | Ollama pas lancé | Relance Ollama depuis le menu Démarrer, ou `ollama serve` |
| `localhost` ne fonctionne pas mais `127.0.0.1` oui | Résolution IPv6 vs IPv4 | Toujours utiliser `127.0.0.1` sur Windows |
| `unknown GGUF file format` | Téléchargement corrompu | Re-télécharge le `.gguf` |
| Réponse très lente | Pas de GPU détecté | `nvidia-smi` doit montrer Ollama. Sinon réinstalle Ollama après le driver NVIDIA |
| Accents cassés dans le terminal | Console en CP1252 | `chcp 65001` avant la commande |

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
