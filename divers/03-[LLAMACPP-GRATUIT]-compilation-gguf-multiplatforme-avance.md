# 03 — llama.cpp (gratuit) : compiler à la main + GGUF, étape par étape

> **Public** : utilisateurs avancés.
> **Coût** : gratuit (modèle local, compilation locale).
> **OS supportés** : Windows (chemin principal), Linux et macOS (sections dédiées).
> **Durée** : 30 à 50 min la première fois, 30 secondes les sessions suivantes.
> **Pré-requis** : matériel GPU (NVIDIA/AMD/Apple Silicon) ou CPU costaud.

> **Lis avant** : [`02-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md`](./02-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md). Si Ollama te suffit, reste sur Ollama. Cette méthode est pour ceux qui veulent compiler eux-mêmes.

---

## Sommaire

1. [C'est quoi CMake et pourquoi compiler ?](#1-cest-quoi-cmake-et-pourquoi-compiler-)
2. [Pré-requis (Windows / macOS / Linux)](#2-pré-requis-windows--macos--linux)
3. [Étape 1 — Cloner `llama.cpp`](#étape-1--cloner-llamacpp)
4. [Étape 2 — Compiler avec CMake (5–15 min)](#étape-2--compiler-avec-cmake-515-min)
5. [Étape 3 — Récupérer un `.gguf` depuis Hugging Face](#étape-3--récupérer-un-gguf-depuis-hugging-face)
6. [Étape 4 — Installer OpenClaude](#étape-4--installer-openclaude)
7. [Étape 5 — Lancer le serveur llama.cpp](#étape-5--lancer-le-serveur-llamacpp)
8. [Étape 6 — Lancer OpenClaude](#étape-6--lancer-openclaude)
9. [Étape 7 — Arrêter](#étape-7--arrêter)
10. [Sessions suivantes](#sessions-suivantes)
11. [Récap ultra-condensé](#récap-ultra-condensé)
12. [Architecture finale](#architecture-finale)
13. [Troubleshooting](#troubleshooting)
14. [Pour aller plus loin](#pour-aller-plus-loin)

---

## 1. C'est quoi CMake et pourquoi compiler ?

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


**CMake** = un outil qui génère des fichiers de build (Makefiles, projets Visual Studio, Ninja…) à partir d'un fichier `CMakeLists.txt`. Il **ne compile pas lui-même**, il prépare le terrain pour MSVC, GCC ou Clang qui font le vrai boulot.

```
CMakeLists.txt  →  CMake (génère)  →  Makefile / projet VS  →  compilateur (cl/gcc/clang)  →  llama-server
```

**Pourquoi compiler `llama.cpp` toi-même** au lieu d'utiliser Ollama ?

| Raison | Explication |
|--------|-------------|
| Optimisations GPU spécifiques | Compile pour **ton** GPU exact (CUDA sm_89/sm_120, ROCm, Metal) |
| Versions très récentes | Toujours la dernière nightly (Ollama est 1-3 mois en retard) |
| Flags expérimentaux | Speculative decoding, KV cache quantification, multi-GPU fin… |
| Pas de surcouche | Tu lances `llama-server` directement, pas de service en arrière-plan |
| Apprentissage | Tu comprends la stack de bout en bout |

**Inconvénient** : 5 à 15 min de compilation et plein de pièges (surtout sous Windows). Si tu n'as pas de besoin pointu, **utilise Ollama** (fichier 02).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 2. Pré-requis (Windows / macOS / Linux)

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 2.1 Communs aux trois OS

| Outil | Test | Pourquoi |
|-------|------|----------|
| Git | `git --version` | Cloner `llama.cpp` |
| Node.js LTS (≥18) | `node --version` | OpenClaude est un package npm |
| Python 3.10+ | `python --version` ou `python3 --version` | Télécharger les `.gguf` via `huggingface_hub` |

### 2.2 Windows

| Outil | Lien | Test |
|-------|------|------|
| **Visual Studio 2022 BuildTools** + workload *Desktop development with C++* + *C++ CMake tools for Windows* | [visualstudio.microsoft.com](https://visualstudio.microsoft.com/downloads/) | `cl` dans Developer PowerShell |
| **CUDA Toolkit 12.x** (si GPU NVIDIA) | [developer.nvidia.com](https://developer.nvidia.com/cuda-downloads) | `nvcc --version` |
| PowerShell 7 (recommandé) | `winget install --id Microsoft.PowerShell` | `pwsh --version` |

Lance le script de vérif :

```powershell
.\scripts\01-check-prereqs.ps1
```

Si une ligne est `[MANQUE]`, installe le composant manquant avant de continuer.

### 2.3 macOS

| Outil | Installation | Test |
|-------|--------------|------|
| Xcode Command Line Tools | `xcode-select --install` | `clang --version` |
| Homebrew | [brew.sh](https://brew.sh) | `brew --version` |
| CMake | `brew install cmake` | `cmake --version` |
| (M1/M2/M3) Metal | inclus avec macOS | rien à faire |

> Sur Apple Silicon (M1/M2/M3), **Metal** est l'accélérateur GPU. `llama.cpp` le détecte tout seul si Xcode CLT est installé.

### 2.4 Linux

#### Debian / Ubuntu (bash)
```bash
sudo apt update
sudo apt install -y git build-essential cmake python3 python3-pip nodejs npm
# Pour GPU NVIDIA : driver + CUDA Toolkit
sudo apt install -y nvidia-cuda-toolkit
```

#### Fedora (bash)
```bash
sudo dnf install -y git gcc gcc-c++ make cmake python3 python3-pip nodejs npm
# CUDA via le repo NVIDIA officiel : voir developer.nvidia.com
```

#### Arch (bash)
```bash
sudo pacman -S --needed git base-devel cmake python python-pip nodejs npm
# CUDA :
sudo pacman -S cuda
```

#### Vérification
```bash
gcc --version
cmake --version
nvcc --version    # uniquement si tu vises CUDA
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

## Étape 1 — Cloner `llama.cpp`

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Windows (PowerShell)
```powershell
cd C:\Users\rehou\Desktop\gemma-agressif-2
.\scripts\02-clone-llama.ps1
```

### macOS / Linux (bash)
```bash
cd ~/projets
git clone https://github.com/ggerganov/llama.cpp.git
cd llama.cpp
```

Durée : 30 s à 1 min.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 2 — Compiler avec CMake (5–15 min)

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


C'est l'étape critique.

### Windows (PowerShell, GPU NVIDIA)

Le script `03-build-llama.ps1` gère **tous** les pièges Windows pour toi :

```powershell
.\scripts\03-build-llama.ps1
```

#### Ce que le script fait en arrière-plan
1. Charge l'environnement MSVC (`vcvars64.bat`).
2. Force le CMake **3.29 de Visual Studio** (pas le standalone qui plante).
3. Tue les processus zombies du build précédent.
4. Supprime `llama.cpp\build` (avec retry).
5. Lance `cmake -B build -DGGML_CUDA=ON -DCMAKE_BUILD_TYPE=Release`.
6. Lance `cmake --build build --config Release -j --target llama-server`.
7. Vérifie la présence de `llama.cpp\build\bin\Release\llama-server.exe`.

#### Sortie attendue
```
=== BUILD OK ===
Name          : llama-server.exe
Length        : 8660992
LastWriteTime : ...
```

### macOS (bash, Apple Silicon avec Metal)

```bash
cd llama.cpp
cmake -B build -DGGML_METAL=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j --target llama-server
```

Le binaire produit : `build/bin/llama-server`.

### Linux (bash, GPU NVIDIA avec CUDA)

```bash
cd llama.cpp
cmake -B build -DGGML_CUDA=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j --target llama-server
```

### Linux (bash, GPU AMD avec ROCm)

```bash
cd llama.cpp
cmake -B build -DGGML_HIP=ON -DAMDGPU_TARGETS=gfx1100 -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j --target llama-server
```

> Adaptez `gfx1100` à votre GPU (ex. `gfx1030` pour RX 6800/6900, `gfx1100` pour RX 7900). Voir [la liste ROCm](https://rocm.docs.amd.com/projects/install-on-linux/en/latest/reference/system-requirements.html).

### Linux (bash, CPU uniquement)

```bash
cd llama.cpp
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j --target llama-server
```

### Si ça plante
Voir [Troubleshooting](#troubleshooting) en bas du fichier.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 3 — Récupérer un `.gguf` depuis Hugging Face

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 3.1 Setup Python + Hugging Face CLI

#### Windows (PowerShell)
```powershell
.\scripts\04-setup-python-hf.ps1
```
Ça crée le venv `.venv`, installe `huggingface_hub`, et lance `hf auth login`.

#### macOS / Linux (bash)
```bash
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade huggingface_hub
hf auth login
```

#### Pendant le login (tous OS)
1. Va sur [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens).
2. **New token** → **Read** → **Generate** → copie le `hf_xxx`.
3. Colle dans le terminal (sur PowerShell, **clic droit** ; sur Mac/Linux, `Cmd+V` / `Ctrl+Shift+V`).
4. Entrée. À `Add token as git credential? (Y/n)` → `n`.

> **Windows** : tu vas voir du rouge — c'est cosmétique (PowerShell 5.1 transforme stderr en faux warnings). Le script continue.

### 3.2 Choisir une quantization selon ta VRAM

| GPU VRAM | Quantization | Taille typique |
|----------|--------------|----------------|
| 12 Go+ | `Q8_0` | ~8 Go |
| 8 Go | `Q6_K_P` ou `Q6_K` | ~6 Go |
| 6 Go ou moins | `Q5_K_M` ou `Q4_K_M` | 4-5 Go |

### 3.3 Télécharger

#### Windows (PowerShell)
```powershell
.\scripts\05b-download-model-q6.ps1
# Le .gguf atterrit dans C:\models\gemma4-e4b\
```

#### macOS / Linux (bash)
```bash
mkdir -p ~/models/gemma4-e4b
hf download <user/repo-gguf> --include "*Q6_K_P.gguf" --local-dir ~/models/gemma4-e4b
```

> Remplacez `<user/repo-gguf>` par le nom du dépôt (ex. `bartowski/gemma-2-9b-it-GGUF`).

#### Si bloqué à 0 octet (tous OS)
Le backend `hf-xet` est buggy. Désactivez-le :

```bash
# Linux / macOS
export HF_HUB_DISABLE_XET=1
```
```powershell
# Windows
$env:HF_HUB_DISABLE_XET = "1"
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

## Étape 4 — Installer OpenClaude

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Windows (PowerShell)
```powershell
.\scripts\06-install-openclaude.ps1
# Ou directement :
npm install -g @gitlawb/openclaude
```

### macOS / Linux (bash)
```bash
npm install -g @gitlawb/openclaude
# Si EACCES : voir le fichier 01, section 4.5 (prefix dans ~/.npm-global)
```

### Vérification (tous OS)
```bash
openclaude --version
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

## Étape 5 — Lancer le serveur llama.cpp

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


C'est ici que **tout s'allume** : le `.gguf` se charge en VRAM, `llama-server` démarre sur le port 8090, expose une API OpenAI-compat.

### Windows (PowerShell)
```powershell
.\scripts\07-start-agent.ps1
```

#### Sortie attendue
```
=== Verifications ===
[OK] server : C:\...\llama-server.exe
[OK] modele : C:\models\gemma4-e4b\Gemma-4-E4B-Uncensored-Q6_K_P.gguf

=== Demarrage de llama-server ===
PID llama-server : 57340
Chargement du modele en VRAM...
[OK] serveur pret : local-gemma

=== Variables d'environnement OpenClaude ===
[OK] OPENAI_BASE_URL = http://127.0.0.1:8090/v1
[OK] OPENAI_API_KEY  = localdev
[OK] OPENAI_MODEL    = local-gemma

============================================================
 SERVEUR PRET sur http://127.0.0.1:8090
 Tape maintenant : openclaude
============================================================
```

### macOS / Linux (bash)

Lance directement le binaire (le serveur reste au premier plan, ouvre un second terminal pour OpenClaude) :

```bash
./build/bin/llama-server \
  --model ~/models/gemma4-e4b/Gemma-4-E4B-Uncensored-Q6_K_P.gguf \
  --host 127.0.0.1 \
  --port 8090 \
  --ctx-size 8192 \
  --n-gpu-layers 99 \
  --alias local-gemma
```

> `--n-gpu-layers 99` = pousse toutes les couches dans le GPU (CUDA / Metal / ROCm selon ta build).

Puis dans un **second terminal**, exporte les variables :

```bash
export OPENAI_BASE_URL=http://127.0.0.1:8090/v1
export OPENAI_API_KEY=localdev
export OPENAI_MODEL=local-gemma
```

### Test (tous OS)
```bash
curl http://127.0.0.1:8090/v1/models -H "Authorization: Bearer localdev"
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

## Étape 6 — Lancer OpenClaude

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


**Dans le terminal où les variables d'environnement sont exportées** :

```bash
openclaude
```

Tu arrives dans l'interface :

```
╭───────────────────────────────────────────╮
│  Welcome to Claude Code (OpenClaude fork) │
│  Using: http://127.0.0.1:8090/v1          │
│  Model: local-gemma                       │
╰───────────────────────────────────────────╯

>
```

Premier test :

```
crée un fichier hello.py qui affiche bonjour
```

L'agent doit créer le fichier dans ton dossier courant.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Étape 7 — Arrêter

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Dans OpenClaude : `Ctrl+C` ou `/exit`.

### Libérer la VRAM

#### Windows (PowerShell)
```powershell
.\scripts\99-stop-agent.ps1
```

#### macOS / Linux (bash)
```bash
# Trouve le PID
pgrep -f llama-server
# Ou tue par nom
pkill -f llama-server
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

## Sessions suivantes

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Plus besoin de tout refaire. Juste :

#### Windows
```powershell
cd C:\Users\rehou\Desktop\gemma-agressif-2
.\scripts\07-start-agent.ps1
openclaude
# Fin :
.\scripts\99-stop-agent.ps1
```

#### macOS / Linux
```bash
cd ~/projets/llama.cpp
./build/bin/llama-server --model ~/models/gemma4-e4b/*.gguf --port 8090 --n-gpu-layers 99 --alias local-gemma &
export OPENAI_BASE_URL=http://127.0.0.1:8090/v1
export OPENAI_API_KEY=localdev
export OPENAI_MODEL=local-gemma
openclaude
# Fin :
pkill -f llama-server
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

## Récap ultra-condensé

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Étape | Action | Durée | Quand |
|-------|--------|-------|-------|
| 0 | Installer pré-requis (git, node, python, compilateur, CUDA/ROCm/Metal) | 30 min | 1 fois |
| 1 | `git clone llama.cpp` | 30 s | 1 fois |
| 2 | Build CMake (CUDA / Metal / ROCm) | **5-15 min** | 1 fois (sauf maj) |
| 3 | Setup Python + télécharger `.gguf` | **5-15 min** | 1 fois par modèle |
| 4 | `npm install -g @gitlawb/openclaude` | 30 s | 1 fois |
| 5 | Lancer `llama-server` | 30 s | chaque session |
| 6 | Lancer `openclaude` | instantané | chaque session |
| 7 | `Ctrl+C` + tuer `llama-server` | 1 s | fin de session |

**Première installation totale : ~30-50 min**
**Sessions suivantes : 30 secondes**

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## Architecture finale

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```
   ┌──────────────┐
   │  Toi (term)  │
   └──────┬───────┘
          │ tape
          ▼
   ┌──────────────┐                  ┌──────────────────────┐
   │  OpenClaude  │ ─── HTTP ──────> │   llama-server       │
   │  (Node CLI)  │   OpenAI-compat  │  (compilé toi-même)  │
   │              │   port 8090      │                      │
   └──────────────┘                  └──────────┬───────────┘
                                                │
                                                │ charge en VRAM
                                                ▼
                                     ┌────────────────────┐
                                     │  Modèle .gguf      │
                                     │  (Gemma uncensored)│
                                     └────────────────────┘
                                                │
                                                ▼
                                     ┌────────────────────────┐
                                     │  GPU (CUDA / Metal /   │
                                     │  ROCm) ou CPU fallback │
                                     └────────────────────────┘
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


### Erreurs build (Windows)

| # | Erreur | Cause | Fix rapide |
|---|--------|-------|-----------|
| 1 | `No CMAKE_ASM_COMPILER could be found` | CMake 4.x standalone vs MASM | Utilise le CMake 3.29 de VS : `& "C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin\cmake.exe"` |
| 2 | `CMakeRCInformation.cmake (rc.exe)` | `vcvars64.bat` pas chargé | Lance depuis Developer PowerShell ou utilise le script `03` |
| 3 | `La ligne entrée est trop longue` | PATH > 8191 caractères | **Ferme** ce terminal et **ouvre-en un neuf** |
| 4 | `NativeCommandError` sur cmake | PS 5.1 traite stderr comme erreur | Utilise `Start-Process` ou PowerShell 7 |
| 5 | `Remove-Item ... en cours d'utilisation` | Process zombies (cl.exe, MSBuild.exe) | `.\scripts\00-clean-build.ps1` puis relance |

### Erreurs build (macOS)

| Erreur | Cause | Fix |
|--------|-------|-----|
| `clang: error: no such file or directory: '/Library/Developer/CommandLineTools'` | Xcode CLT manquant | `xcode-select --install` |
| `cmake: command not found` | Pas installé | `brew install cmake` |
| Compilation lente, pas de GPU | Metal pas activé | Ajoute `-DGGML_METAL=ON` au cmake |

### Erreurs build (Linux)

| Erreur | Cause | Fix |
|--------|-------|-----|
| `gcc: command not found` | Compilateur manquant | `sudo apt install build-essential` (Debian) |
| `nvcc: command not found` | CUDA Toolkit pas installé | `sudo apt install nvidia-cuda-toolkit` (Debian) |
| `Could NOT find CUDAToolkit` | CUDA installé mais pas dans le PATH | `export PATH=/usr/local/cuda/bin:$PATH` |
| Compilation OK mais 0 layer GPU au runtime | Driver NVIDIA absent ou cassé | `nvidia-smi` doit répondre. Sinon réinstalle les drivers |

### Erreurs Hugging Face / téléchargement

| Erreur | Cause | Fix |
|--------|-------|-----|
| `hf : To log in...` en rouge (Windows) | Cosmétique PS 5.1 | Ignore le rouge, colle ton token et continue |
| Téléchargement HF bloqué à 0 octet | Backend `hf-xet` buggy | `HF_HUB_DISABLE_XET=1` (déjà dans le script `05`) |
| `403 Forbidden` | Modèle gated, accord pas signé | Va sur la page HF du modèle, clique « Access repository » |

### Erreurs runtime

| # | Erreur | Cause | Fix |
|---|--------|-------|-----|
| 1 | OpenClaude `Input must be provided` | TTY non détecté | Lance `openclaude` directement dans ton shell, **pas depuis un script** |
| 2 | `couldn't bind HTTP server socket port 8090` | Port pris (souvent Java) | Linux/Mac : `lsof -i :8090`. Windows : `Get-NetTCPConnection -LocalPort 8090 -State Listen`. Change le port |
| 3 | `llama-server` crash silencieux | OOM VRAM (Q8 sur 8 Go par ex.) | Utilise une quantization plus légère (Q6, Q5), ou réduis `--ctx-size` (ex. `4096`) |
| 4 | Réponses très lentes (CPU only) | GPU non détecté | Vérifie le build : doit contenir `GGML_CUDA`/`GGML_METAL`/`GGML_HIP` ; vérifie `--n-gpu-layers 99` |

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


- [`01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md`](./01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md) — installation OpenClaude (Ollama / LM Studio)
- [`02-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md`](./02-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md) — alternative simple (Ollama)
- [`04-[OPENCLAUDE-GRATUIT]-agent-local-avance-llamacpp-compile-soi-meme.md`](./04-[OPENCLAUDE-GRATUIT]-agent-local-avance-llamacpp-compile-soi-meme.md) — version verbose avec tuning, multi-GPU, optimisations avancées
- [`scripts/README.md`](../scripts/README.md) — tableau synthétique des scripts PowerShell

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>
