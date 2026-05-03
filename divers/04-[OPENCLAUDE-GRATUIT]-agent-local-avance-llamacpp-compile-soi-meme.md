# 04 — Agent local avancé (gratuit) : OpenClaude + llama.cpp compilé à la main

> **Public** : utilisateurs avancés (power users).
> **Coût** : gratuit (compilation locale, modèle local).
> **OS supportés** : Windows, macOS, Linux.
> **Durée** : 30 min à 1 h pour le premier setup.
> **Pré-requis** : compilateur C++, GPU recommandé.

Pour les utilisateurs **avancés** qui veulent compiler `llama.cpp` eux-mêmes au lieu de passer par Ollama ou LM Studio. Plus de contrôle, plus d'options pointues, mais setup plus long.

> **À lire avant** : si tu veux juste que ça marche, prends le guide [`01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md`](./01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md). Pour la version courte de ce qui est ici, voir [`03-[LLAMACPP-GRATUIT]-compilation-gguf-multiplatforme-avance.md`](./03-[LLAMACPP-GRATUIT]-compilation-gguf-multiplatforme-avance.md).

---

## Sommaire

- [1. Pourquoi compiler llama.cpp soi-même](#1-pourquoi-compiler-llamacpp-soi-même)
- [2. Pré-requis par OS](#2-pré-requis-par-os)
- [3. Pièges Windows à connaître](#3-pièges-windows-à-connaître)
- [4. Compiler llama.cpp avec accélération GPU](#4-compiler-llamacpp-avec-accélération-gpu)
- [5. Télécharger un modèle GGUF](#5-télécharger-un-modèle-gguf)
- [6. Lancer llama-server](#6-lancer-llama-server)
- [7. Installer OpenClaude](#7-installer-openclaude)
- [8. Configuration et lancement de l'agent](#8-configuration-et-lancement-de-lagent)
- [9. Tester l'agent](#9-tester-lagent)
- [10. Tuning et optimisation](#10-tuning-et-optimisation)
- [11. Troubleshooting](#11-troubleshooting)
- [12. Stack technique complète](#12-stack-technique-complète)

---

## 1. Pourquoi compiler llama.cpp soi-même

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


`llama.cpp` est le **moteur d'inférence** sur lequel tournent Ollama et LM Studio (en arrière-plan). Le compiler directement te donne :

| Bénéfice | Explication |
|----------|-------------|
| **Optimisations matérielles fines** | Active CUDA 12.x, ROCm (AMD), Metal (Apple Silicon), Vulkan, Sycl... selon ton GPU |
| **Architecture CUDA spécifique** | Compile pour **ton** SM exact (sm_89 RTX 40xx, sm_120 RTX 50xx) au lieu d'un binaire générique |
| **Flags expérimentaux** | Speculative decoding, draft models, multi-GPU, KV cache quantification, flash attention |
| **Versions très récentes** | Tu utilises la dernière nightly de `llama.cpp` (Ollama est souvent 1-3 mois en retard) |
| **Pas de dépendance Ollama/LM Studio** | Pas de service en arrière-plan, pas de surcouche |
| **Apprentissage** | Tu comprends la stack de bout en bout |

**Inconvénient principal** : le build prend 10-30 min et il y a des pièges (surtout sous Windows). Si tu n'as pas un besoin très spécifique, **utilise Ollama** (cf. [fichier 01](./01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md)).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 2. Pré-requis par OS

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 2.1 Windows

| Outil | Installation | Vérification |
|-------|--------------|--------------|
| [Git](https://git-scm.com/download/win) | exe standard | `git --version` |
| [Node.js LTS](https://nodejs.org/) | exe standard | `node --version` |
| [Python 3.10+](https://www.python.org/downloads/windows/) | exe standard, **coche *Add to PATH*** | `python --version` |
| [Visual Studio 2022 BuildTools](https://visualstudio.microsoft.com/downloads/) | workload *Desktop development with C++* + *C++ CMake tools for Windows* | `cl` visible depuis *Developer PowerShell* |
| [CUDA Toolkit 12.x](https://developer.nvidia.com/cuda-downloads) | exe NVIDIA (si GPU NVIDIA) | `nvcc --version` |
| [PowerShell 7](https://github.com/PowerShell/PowerShell/releases) | `winget install --id Microsoft.PowerShell` | `pwsh --version` |

### 2.2 macOS

| Outil | Installation | Vérification |
|-------|--------------|--------------|
| Xcode Command Line Tools | `xcode-select --install` | `clang --version` |
| Homebrew | [brew.sh](https://brew.sh/) | `brew --version` |
| Git | `brew install git` (ou via Xcode) | `git --version` |
| Node.js LTS | `brew install node` | `node --version` |
| Python 3.10+ | `brew install python@3.11` | `python3 --version` |
| CMake | `brew install cmake` | `cmake --version` |

Sur Mac Apple Silicon (M1/M2/M3/M4), pas besoin de CUDA — `llama.cpp` utilise **Metal** automatiquement (intégré au framework Apple).

### 2.3 Linux (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install -y git build-essential cmake python3 python3-pip python3-venv \
                     curl ca-certificates

# Node.js LTS
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs
```

Pour CUDA (NVIDIA) :

```bash
# Suivre la procédure officielle pour ta distrib :
# https://developer.nvidia.com/cuda-downloads
nvcc --version
```

Pour ROCm (AMD) :

```bash
# Voir https://rocm.docs.amd.com/projects/install-on-linux/
sudo apt install -y rocm-hip-sdk
```

### 2.4 Linux (Fedora)

```bash
sudo dnf install -y git cmake gcc-c++ python3 python3-pip nodejs
```

CUDA : suivre [la doc NVIDIA](https://developer.nvidia.com/cuda-downloads) selon la version de Fedora.

### 2.5 Linux (Arch)

```bash
sudo pacman -S git base-devel cmake python python-pip nodejs npm
sudo pacman -S cuda    # si NVIDIA
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

## 3. Pièges Windows à connaître

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Cette section corrige **4 problèmes** qui font planter la plupart des tutoriels existants sous Windows. Sur macOS et Linux, ces problèmes n'existent pas — **passe directement à la section 4**.

### Piège 1 — CMake 4.x casse la détection MASM

Si tu as à la fois :

- CMake standalone ≥ 4.0 (`C:\Program Files\CMake\bin\cmake.exe`)
- CMake 3.29 livré avec VS BuildTools

Le CMake standalone fait échouer ggml avec :

```
-- The ASM compiler identification is unknown
-- Didn't find assembler
CMake Error: No CMAKE_ASM_COMPILER could be found.
```

**Correction** : utiliser explicitement le CMake 3.29 de VS BuildTools :

```powershell
$vsCmake = "C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin\cmake.exe"
& $vsCmake --version   # doit afficher 3.29.x
```

### Piège 2 — Environnement MSVC non chargé

Si tu n'as pas lancé `vcvars64.bat`, CMake ne trouve pas `rc.exe` et erreurs en cascade :

```
CMakeRCInformation.cmake:14 (get_filename_component):
  get_filename_component called with incorrect number of arguments
```

**Correction** : charger `vcvars64.bat` au début de chaque session de build, ou utiliser **Developer PowerShell for VS 2022** depuis le menu Démarrer.

### Piège 3 — `La ligne entrée est trop longue`

Si tu relances `vcvars64.bat` plusieurs fois dans la même session, le `PATH` gonfle jusqu'à dépasser **8191 caractères**, et `cmd.exe` refuse :

```
cmd : La ligne entrée est trop longue.
```

**Correction** : détecter si MSVC est déjà chargé (présence de `cl.exe`), sinon réinitialiser `PATH` à la base système HKLM+HKCU avant de charger vcvars. Le script `scripts/03-build-llama.ps1` du projet le fait automatiquement.

### Piège 4 — PowerShell 5.1 transforme stderr en erreur terminale

Avec `$ErrorActionPreference = "Stop"` (pratique courante), **toute** sortie stderr d'une commande native provoque `NativeCommandError` / `RemoteException`, même si la commande a **réussi** (exit code 0). Exemple typique :

```
cmake.exe : CMAKE_BUILD_TYPE=Release      ← juste un message status
+ CategoryInfo : NotSpecified: (CMAKE_BUILD_TYPE=Release:String) [], RemoteException
+ FullyQualifiedErrorId : NativeCommandError
```

Ni `2>&1`, ni `| Out-Host`, ni `cmd /c` ne suffisent en PS 5.1.

**Corrections** :

1. **`Start-Process -NoNewWindow -Wait -PassThru`** pour les appels critiques. PowerShell ne voit **jamais** le stderr du processus fils, seulement `ExitCode`.
2. Pour les autres (`pip`, `npm`, `hf`) : `$ErrorActionPreference = "Continue"` + vérification explicite de `$LASTEXITCODE` après chaque appel.
3. En **PowerShell 7+** : `$PSNativeCommandUseErrorActionPreference = $false` désactive en bloc ce comportement.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 4. Compiler llama.cpp avec accélération GPU

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 4.1 Cloner le dépôt

```bash
# Tous OS
git clone https://github.com/ggml-org/llama.cpp
cd llama.cpp
```

### 4.2 Build — Windows + CUDA

#### Via le script du projet (recommandé)

Si tu as cloné ce repo `gemma-agressif-2`, utilise le script automatisé :

```powershell
cd C:\Users\rehou\Desktop\gemma-agressif-2
.\scripts\03-build-llama.ps1
```

Le script :

1. vérifie les chemins (vcvars, VS CMake, CUDA, llama.cpp)
2. détecte si MSVC est déjà chargé, sinon reset PATH + charge `vcvars64.bat`
3. fixe `$env:CUDA_PATH = "C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.9"`
4. tue les processus zombies d'un build précédent
5. supprime `llama.cpp\build` (avec retry x5 si fichiers verrouillés)
6. lance `cmake -B build -DGGML_CUDA=ON -DCMAKE_BUILD_TYPE=Release` via `Start-Process`
7. lance `cmake --build build --config Release -j --target llama-server` via `Start-Process`
8. vérifie la présence de `llama.cpp\build\bin\Release\llama-server.exe`

#### Manuellement, pour comprendre

```powershell
# Ouvrir Developer PowerShell for VS 2022 depuis le menu Démarrer

$vsCmake  = "C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\Common7\IDE\CommonExtensions\Microsoft\CMake\CMake\bin\cmake.exe"
$env:CUDA_PATH = "C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.9"

cd <chemin>\llama.cpp
if (Test-Path build) { Remove-Item -Recurse -Force build }

Start-Process -FilePath $vsCmake `
  -ArgumentList @("-B","build","-DGGML_CUDA=ON","-DCMAKE_BUILD_TYPE=Release") `
  -NoNewWindow -Wait

Start-Process -FilePath $vsCmake `
  -ArgumentList @("--build","build","--config","Release","-j","--target","llama-server") `
  -NoNewWindow -Wait
```

Binaire produit : `llama.cpp\build\bin\Release\llama-server.exe`

### 4.3 Build — macOS (Metal automatique)

Sur Apple Silicon, CMake détecte Metal automatiquement. Aucun flag spécial.

```bash
cd llama.cpp
cmake -B build
cmake --build build --config Release -j --target llama-server
```

Binaire produit : `build/bin/llama-server`

Pour vérifier que Metal est bien activé :

```bash
./build/bin/llama-server --help | grep -i metal
```

Sur Mac Intel (sans Metal performant), tu peux compiler en CPU pur (le défaut suffit) ou avec OpenBLAS pour accélérer :

```bash
brew install openblas
cmake -B build -DGGML_BLAS=ON -DGGML_BLAS_VENDOR=OpenBLAS
cmake --build build --config Release -j
```

### 4.4 Build — Linux + CUDA (NVIDIA)

```bash
cd llama.cpp
cmake -B build -DGGML_CUDA=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j --target llama-server
```

Binaire produit : `build/bin/llama-server`

Si CMake ne trouve pas CUDA :

```bash
export CUDA_PATH=/usr/local/cuda
export PATH=$CUDA_PATH/bin:$PATH
nvcc --version    # doit répondre

cmake -B build -DGGML_CUDA=ON \
               -DCMAKE_CUDA_COMPILER=$CUDA_PATH/bin/nvcc \
               -DCMAKE_BUILD_TYPE=Release
```

### 4.5 Build — Linux + ROCm (AMD)

```bash
cd llama.cpp
cmake -B build -DGGML_HIP=ON -DAMDGPU_TARGETS=gfx1100 -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j --target llama-server
```

Remplace `gfx1100` par la cible ROCm de ta carte (RX 7900 = `gfx1100`, RX 6800 = `gfx1030`...).

### 4.6 Build — Linux + Vulkan (universel GPU)

Pour tout GPU (NVIDIA, AMD, Intel) sans drivers spécifiques :

```bash
sudo apt install -y vulkan-tools libvulkan-dev glslc
cmake -B build -DGGML_VULKAN=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j --target llama-server
```

### 4.7 Forcer une architecture CUDA précise

Si tu connais le SM exact de ta carte, tu réduis le temps de build et la taille du binaire :

| GPU | SM (Compute Capability) |
|-----|--------------------------|
| RTX 20xx (Turing) | 75 |
| RTX 30xx (Ampere) | 86 |
| RTX 40xx (Ada Lovelace) | 89 |
| RTX 50xx (Blackwell) | 120 |
| H100 (Hopper) | 90 |

Exemple pour RTX 4070 :

```bash
cmake -B build -DGGML_CUDA=ON -DCMAKE_CUDA_ARCHITECTURES=89 -DCMAKE_BUILD_TYPE=Release
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

## 5. Télécharger un modèle GGUF

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 5.1 Avec `huggingface_hub` (tous OS)

#### Setup Python

```bash
# Tous OS
python -m venv .venv

# Activation
# Windows PowerShell
.\.venv\Scripts\Activate.ps1
# macOS / Linux
source .venv/bin/activate

# Install
python -m pip install --upgrade pip
python -m pip install -U huggingface_hub
```

#### Authentification

Génère un token sur [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens) (lecture seule suffit).

```bash
hf auth login
# Colle ton token quand demandé
```

#### Télécharger le modèle

**Windows** :

```powershell
mkdir C:\models\gemma4-e4b -Force
hf download HauhauCS/Gemma-4-E4B-Uncensored-HauhauCS-Aggressive `
  Gemma-4-E4B-Uncensored-HauhauCS-Aggressive-Q8_K_P.gguf `
  --local-dir C:\models\gemma4-e4b
```

**macOS / Linux** :

```bash
mkdir -p ~/models/gemma4-e4b
hf download HauhauCS/Gemma-4-E4B-Uncensored-HauhauCS-Aggressive \
  Gemma-4-E4B-Uncensored-HauhauCS-Aggressive-Q8_K_P.gguf \
  --local-dir ~/models/gemma4-e4b
```

### 5.2 Choix du quantization

Pour comprendre Q4 vs Q6 vs Q8 et les suffixes K_M / K_P, voir [`docs/00-installation-windows-ollama-gguf.md`](../00-installation-windows-ollama-gguf.md#comprendre-les-quantizations-q4-q6-q8-k_m-k_p-avant-de-télécharger).

Récapitulatif rapide :

| Quant | Qualité | Taille / VRAM | Usage recommandé |
|-------|---------|---------------|------------------|
| `Q8_K_P` | Max (~99.5%) | Lourd (~5 Go) | Qualité max, GPU 10 Go+ |
| `Q6_K_P` | Excellente (~98%) | Équilibré (~3.5 Go) | Sweet spot RTX 4070 8 Go |
| `Q5_K_M` | Très bonne (~96%) | Léger (~3 Go) | GPU 6 Go |
| `Q4_K_M` | Bonne (~92%) | Très léger (~2.5 Go) | CPU only ou 4 Go VRAM |

### 5.3 Bug `hf-xet` sous Windows

Sur Windows, depuis `huggingface_hub >= 0.25`, le backend `hf-xet` peut bloquer le téléchargement à 0 octet. Désactive-le avant `hf download` :

```powershell
$env:HF_HUB_DISABLE_XET = "1"
hf download ...
```

Documenté dans la section [Troubleshooting](#11-troubleshooting).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 6. Lancer llama-server

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 6.1 Windows

```powershell
$server = "C:\Users\rehou\Desktop\gemma-agressif-2\llama.cpp\build\bin\Release\llama-server.exe"
$model  = "C:\models\gemma4-e4b\Gemma-4-E4B-Uncensored-HauhauCS-Aggressive-Q8_K_P.gguf"

& $server `
  -m $model `
  --alias local-gemma `
  --jinja `
  -ngl 999 `
  -c 8192 `
  -fa on `
  -ctk q8_0 -ctv q8_0 `
  --host 127.0.0.1 `
  --port 8090 `
  --api-key localdev
```

### 6.2 macOS / Linux

```bash
SERVER=./llama.cpp/build/bin/llama-server
MODEL=$HOME/models/gemma4-e4b/Gemma-4-E4B-Uncensored-HauhauCS-Aggressive-Q8_K_P.gguf

$SERVER \
  -m $MODEL \
  --alias local-gemma \
  --jinja \
  -ngl 999 \
  -c 8192 \
  -fa on \
  -ctk q8_0 -ctv q8_0 \
  --host 127.0.0.1 \
  --port 8090 \
  --api-key localdev
```

### 6.3 Explication des paramètres importants

| Param | Valeur ex. | Effet |
|-------|------------|-------|
| `-m` | chemin `.gguf` | Le fichier modèle à charger |
| `--alias` | `local-gemma` | Nom logique exposé via l'API |
| `--jinja` | (flag) | Active le templating Jinja pour les chat templates modernes |
| `-ngl` | `999` | Nombre de couches offloadées sur GPU. 999 = toutes |
| `-c` | `8192` | Taille de contexte en tokens. Plus = plus de VRAM |
| `-fa` | `on` | Flash Attention (réduit drastiquement la VRAM du KV cache) |
| `-ctk q8_0` | `q8_0` | Quantification du KV cache pour les **clés** (économise VRAM) |
| `-ctv q8_0` | `q8_0` | Quantification du KV cache pour les **valeurs** |
| `--host` | `127.0.0.1` | N'écoute que en local (pas exposé au réseau) |
| `--port` | `8090` | Port d'écoute (8080 souvent occupé par d'autres apps) |
| `--api-key` | `localdev` | Clé d'auth bidon mais requise par l'API |

### 6.4 Vérifier que le serveur répond

Dans un autre terminal :

```bash
# Tous OS
curl http://127.0.0.1:8090/v1/models -H "Authorization: Bearer localdev"
```

Tu reçois un JSON avec `local-gemma` listé.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 7. Installer OpenClaude

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
# Tous OS
npm install -g @gitlawb/openclaude
openclaude --version
```

Pour les détails (permissions Mac/Linux, prefix npm), voir [`01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md` section 4.5](./01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md#45-installer-openclaude).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 8. Configuration et lancement de l'agent

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 8.1 Variables d'environnement

#### Windows PowerShell

```powershell
$env:OPENAI_API_KEY = "localdev"
$env:OPENAI_BASE_URL = "http://127.0.0.1:8090/v1"
$env:OPENAI_MODEL = "local-gemma"
```

#### macOS / Linux

```bash
export OPENAI_API_KEY=localdev
export OPENAI_BASE_URL=http://127.0.0.1:8090/v1
export OPENAI_MODEL=local-gemma
```

### 8.2 Lancement

```bash
# Tous OS
openclaude
```

Tu arrives dans le chat OpenClaude.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 9. Tester l'agent

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Test 1 — Chat basique

```
dis-moi bonjour en français
```

### Test 2 — Création de fichier

```
crée un fichier hello.py qui affiche "Hello from local llama.cpp"
```

### Test 3 — Refacto

Crée un fichier `bad.py` :

```python
def f(x,y):
  return x if x>y else y
```

Puis dans OpenClaude :

```
refactor bad.py : nomme la fonction `maximum`, ajoute des type hints, une docstring en français
```

### Test 4 — Capacité shell

```
quel est mon répertoire courant ? quels fichiers contient-il ?
```

OpenClaude propose `pwd` + `ls` (ou équivalents Windows) et te demande confirmation.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 10. Tuning et optimisation

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 10.1 Si tu manques de VRAM

| Symptôme | Solution |
|----------|----------|
| OOM au chargement | Réduis `-ngl` (ex: `-ngl 24` au lieu de `999`) — moins de couches sur GPU |
| OOM en cours d'inférence | Réduis `-c` (contexte) à 4096 ou 2048 |
| OOM avec gros contexte | Active `-fa on` + `-ctk q8_0 -ctv q8_0` (déjà dans la commande de la sec 6) |
| Toujours OOM | Prends une quantization plus petite (Q4_K_M au lieu de Q8) |

### 10.2 Si l'inférence est lente

| Symptôme | Solution |
|----------|----------|
| < 10 tok/s sur GPU NVIDIA | Vérifie `nvidia-smi` — ollama ou autre process bloque la VRAM ? |
| Lent au premier token (TTFT) | Active `-fa on` (flash attention) |
| Lent en milieu de génération | Active `--cont-batching` pour traitement parallèle |
| Décharge sur CPU détectée | Réduis `-ngl` ou prends plus petit quant |

### 10.3 Speculative decoding (avancé)

Tu peux accélérer un gros modèle en utilisant un petit modèle "draft" qui propose des tokens, validés par le gros :

```bash
$SERVER -m gros-modele.gguf \
        -md petit-modele.gguf \
        --draft 8 \
        ...
```

Souvent +30-50 % de débit pour 0 perte de qualité.

### 10.4 Multi-GPU

Si tu as plusieurs GPU :

```bash
$SERVER -m modele.gguf \
        -ngl 999 \
        -ts 0.5,0.5 \    # split 50/50 entre GPU 0 et GPU 1
        ...
```

### 10.5 Augmenter le contexte au-delà du native

Certains modèles supportent l'extension de contexte via RoPE scaling :

```bash
$SERVER -m modele.gguf \
        -c 32768 \
        --rope-scaling yarn \
        --rope-freq-scale 0.25 \
        ...
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

## 11. Troubleshooting

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### 11.1 `No CMAKE_ASM_COMPILER could be found` (Windows)

→ Tu utilises CMake 4.x standalone. Force le CMake 3.29 de VS BuildTools (cf. [Piège 1](#piège-1--cmake-4x-casse-la-détection-masm)).

### 11.2 `request exceeds context size`

→ Augmente `-c 8192` à `-c 32768` dans la commande llama-server. Mais ça consomme plus de VRAM.

### 11.3 `La ligne entrée est trop longue` (Windows)

→ Ta session PowerShell a un PATH gonflé. **Ferme** cette fenêtre, **ouvre-en une neuve**, et relance.

### 11.4 `couldn't bind HTTP server socket, port 8080`

→ Le port 8080 est pris (souvent par Java, Tomcat, ou un autre service). Change avec `--port 8090` (ou autre).

Vérifier qui utilise un port :

```bash
# Windows
Get-NetTCPConnection -LocalPort 8080 -State Listen

# macOS / Linux
lsof -i :8080
```

### 11.5 `error: unknown value for --flash-attn: '-ctk'`

→ La syntaxe `-fa` change selon la version de llama.cpp. Maintenant il faut un argument explicite : `-fa on` (et pas juste `-fa`).

### 11.6 OpenClaude `Input must be provided either through stdin or as a prompt argument`

→ TTY non détecté. Lance OpenClaude **directement depuis ton terminal interactif**, pas depuis un script PowerShell.

### 11.7 OpenClaude utilise un ancien modèle

→ Config `~/.claude.json` qui prend le dessus sur tes variables d'env. Voir [`01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md` section 9](./01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md#9-configuration-globale-openclaude).

### 11.8 Test brut du serveur sans OpenClaude

```bash
# Windows
curl.exe http://127.0.0.1:8090/v1/models `
  -H "Authorization: Bearer localdev"

curl.exe http://127.0.0.1:8090/v1/chat/completions `
  -H "Content-Type: application/json" `
  -H "Authorization: Bearer localdev" `
  -d '{\"model\":\"local-gemma\",\"messages\":[{\"role\":\"user\",\"content\":\"Dis bonjour\"}]}'
```

```bash
# macOS / Linux
curl http://127.0.0.1:8090/v1/models \
  -H "Authorization: Bearer localdev"

curl http://127.0.0.1:8090/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer localdev" \
  -d '{"model":"local-gemma","messages":[{"role":"user","content":"Dis bonjour"}]}'
```

Si ces deux requêtes répondent → le serveur fonctionne, le problème est côté OpenClaude (config ou variables d'env).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 12. Stack technique complète

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Composants

| Brique | Rôle | Version recommandée |
|--------|------|---------------------|
| [`llama.cpp`](https://github.com/ggml-org/llama.cpp) | Serveur d'inférence OpenAI-compatible | nightly (master) |
| [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads) | Backend GPU NVIDIA | 12.9+ |
| [Visual Studio BuildTools](https://visualstudio.microsoft.com/downloads/) | Compilateur MSVC + MASM (Windows seulement) | 2022 |
| [CMake](https://cmake.org/download/) | Génération de build | 3.29 (bundled VS) |
| [Node.js](https://nodejs.org/) | Runtime pour OpenClaude | 18+ LTS |
| [@gitlawb/openclaude](https://www.npmjs.com/package/@gitlawb/openclaude) | CLI agent | dernière |
| Modèle GGUF | Le « cerveau » | Gemma-4-E4B Uncensored Q6_K_P |

### Schéma

```
   ┌─────────────────────────────────────────────────────────────────┐
   │  Ton OS (Windows / macOS / Linux)                               │
   │                                                                  │
   │   ┌──────────────┐         ┌────────────────────┐               │
   │   │  OpenClaude  │ ──API── │   llama-server     │               │
   │   │   (Node.js)  │  HTTP   │   (compilé local)  │               │
   │   │   Terminal   │         │   port 8090        │               │
   │   └──────────────┘         └─────────┬──────────┘               │
   │                                      │                           │
   │                                      │ charge en VRAM            │
   │                                      ▼                           │
   │                              ┌──────────────────┐                │
   │                              │  Modèle GGUF     │                │
   │                              │  (Gemma 4-E4B    │                │
   │                              │   Uncensored)    │                │
   │                              └──────────────────┘                │
   │                                      │                           │
   │                                      │ inférence GPU             │
   │                                      ▼                           │
   │   ┌────────────┐  ┌────────────┐  ┌────────────┐                │
   │   │   CUDA     │  │   Metal    │  │   ROCm /   │                │
   │   │  (NVIDIA)  │  │  (Apple)   │  │  Vulkan    │                │
   │   └────────────┘  └────────────┘  └────────────┘                │
   │                                                                  │
   └─────────────────────────────────────────────────────────────────┘
```

### Pour les sessions suivantes

Une fois la première installation faite, tu n'as plus qu'à relancer le serveur + OpenClaude :

```powershell
# Windows
.\scripts\07-start-agent.ps1
```

```bash
# macOS / Linux
./build/bin/llama-server -m <chemin> --port 8090 --alias local-gemma -ngl 999 &
export OPENAI_BASE_URL=http://127.0.0.1:8090/v1
export OPENAI_API_KEY=localdev
export OPENAI_MODEL=local-gemma
openclaude
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

## Liens utiles

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


- [`01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md`](./01-[OPENCLAUDE-GRATUIT]-installation-multiplatforme-ollama-lmstudio.md) — la version simple (Ollama / LM Studio)
- [`02-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md`](./02-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md) — Ollama en quickstart
- [`03-[LLAMACPP-GRATUIT]-compilation-gguf-multiplatforme-avance.md`](./03-[LLAMACPP-GRATUIT]-compilation-gguf-multiplatforme-avance.md) — version concise du présent document
- [Documentation officielle llama.cpp](https://github.com/ggml-org/llama.cpp/blob/master/README.md)
- [llama.cpp server docs](https://github.com/ggml-org/llama.cpp/blob/master/tools/server/README.md)

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>
