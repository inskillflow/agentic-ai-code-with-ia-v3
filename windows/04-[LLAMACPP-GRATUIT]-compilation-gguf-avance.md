# 04 — llama.cpp (gratuit, avancé) sur **Windows** : compilation et run de modèles GGUF

> **Public** : avancé — tu es à l'aise avec PowerShell, `git`, `cmake`.
> **Coût** : 100 % gratuit.
> **OS** : Windows 10 / 11.
> **Durée** : 30 à 60 min selon ta machine.
> **Pré-requis** : 8 Go RAM minimum. GPU NVIDIA recommandé pour la performance (CUDA).
> **Terminal** : PowerShell 7 recommandé.

> Pour Linux → [`../linux/04-[LLAMACPP-GRATUIT]-...`](../linux/04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md).
> Pour macOS → [`../macos/04-[LLAMACPP-GRATUIT]-...`](../macos/04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md).

> **Pas envie de compiler ?** Reste sur Ollama (tuto 01) qui utilise déjà llama.cpp en interne avec une couche de confort. Ce tuto te donne le **contrôle total** : version, optimisations CUDA spécifiques, modèles exotiques.

---

## Sommaire

1. [Pourquoi compiler llama.cpp soi-même ?](#1-pourquoi-compiler-llamacpp-soi-même-)
2. [Pré-requis](#2-pré-requis)
3. [Cloner et compiler](#3-cloner-et-compiler)
4. [Vérifier la compilation](#4-vérifier-la-compilation)
5. [Récupérer un modèle GGUF](#5-récupérer-un-modèle-gguf)
6. [Test 1 : `llama-cli` (one-shot)](#6-test-1--llama-cli-one-shot)
7. [Test 2 : `llama-server` (API HTTP compatible OpenAI)](#7-test-2--llama-server-api-http-compatible-openai)
8. [Mesurer la performance](#8-mesurer-la-performance)
9. [Mise à jour de llama.cpp](#9-mise-à-jour-de-llamacpp)
10. [Cheatsheet](#10-cheatsheet)
11. [Troubleshooting](#troubleshooting)
12. [Suite](#suite)

---

## 1. Pourquoi compiler llama.cpp soi-même ?

| Avantage | Quand c'est utile |
|----------|-------------------|
| Optimisations CPU spécifiques (AVX-512, etc.) | CPU récent sans GPU |
| Choisir précisément l'accélérateur (CUDA/Vulkan) | Mix CUDA/CPU, fallback Vulkan |
| Dernière version (release ou main) | Bug fixé d'hier, support nouveau modèle |
| Binaires utilitaires : `llama-quantize`, `llama-bench` | Quantizer toi-même un FP16 → Q4_K_M |
| Server HTTP exposé directement | Branché sur OpenClaude (cf. tuto 05) |

[↑ Sommaire](#sommaire)

---

## 2. Pré-requis

### Communs

```powershell
# Toolchain via winget
winget install Git.Git
winget install Kitware.CMake
winget install Microsoft.VisualStudio.2022.BuildTools --silent --override "--wait --add Microsoft.VisualStudio.Workload.VCTools --includeRecommended"
```

> Le **VS Build Tools** apporte `cl.exe`, le compilateur C++ MSVC nécessaire à CMake sur Windows.

Vérifie dans une **nouvelle** PowerShell (après les installs) :
```powershell
git --version
cmake --version
```

Pour `cl.exe`, tu dois soit :
- ouvrir une **« Developer PowerShell for VS 2022 »** depuis le menu Démarrer (recommandé) ;
- OU lancer `vcvars64.bat` dans ta session PowerShell.

### Variante GPU NVIDIA (CUDA)

1. Vérifie le GPU et le driver :
```powershell
nvidia-smi
```

2. Installe le **CUDA Toolkit** (qui contient `nvcc`) :
   - Télécharge depuis [developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads) (choisir **Windows / x86_64 / 11 ou 10 / exe (local)**).
   - Lance l'installeur, choix « Express ».
3. Vérifie :
```powershell
nvcc --version
```

### Variante CPU uniquement

Rien de plus. Les optimisations AVX/AVX2 sont automatiques.

[↑ Sommaire](#sommaire)

---

## 3. Cloner et compiler

Ouvre **« Developer PowerShell for VS 2022 »** (sinon `cl.exe` ne sera pas dans le PATH).

```powershell
cd $env:USERPROFILE
git clone https://github.com/ggml-org/llama.cpp.git
cd llama.cpp
```

### Build CUDA (NVIDIA)

```powershell
cmake -B build -DGGML_CUDA=ON
cmake --build build --config Release -j 8
```

### Build CPU uniquement

```powershell
cmake -B build
cmake --build build --config Release -j 8
```

### Build Vulkan (alternative GPU généraliste)

```powershell
# Installe d'abord le Vulkan SDK depuis https://vulkan.lunarg.com/
cmake -B build -DGGML_VULKAN=ON
cmake --build build --config Release -j 8
```

La compilation prend 10 à 30 min sur Windows.

[↑ Sommaire](#sommaire)

---

## 4. Vérifier la compilation

```powershell
dir build\bin\Release\llama-*.exe
```

Tu dois voir au moins :
- `llama-cli.exe` — exécution one-shot ou interactive
- `llama-server.exe` — serveur HTTP compatible OpenAI
- `llama-bench.exe` — benchmark
- `llama-quantize.exe` — quantizer un FP16 vers Q4/Q6/Q8

Test rapide :
```powershell
.\build\bin\Release\llama-cli.exe --version
```

> Note : sur Windows avec CMake et le générateur Visual Studio, les binaires sont dans `build\bin\Release\` (sous-dossier `Release`). Sur Linux/macOS, c'est `build/bin/`.

[↑ Sommaire](#sommaire)

---

## 5. Récupérer un modèle GGUF

Va sur HuggingFace, cherche un modèle GGUF (ex. [Qwen2.5-Coder-7B-Instruct-GGUF](https://huggingface.co/bartowski/Qwen2.5-Coder-7B-Instruct-GGUF)).

Choisis une quantization selon ta VRAM :
- 4 Go → `*-Q4_K_M.gguf`
- 8 Go → `*-Q6_K.gguf`
- 12 Go+ → `*-Q8_0.gguf`

Télécharge avec PowerShell :

```powershell
mkdir $env:USERPROFILE\models -Force
cd $env:USERPROFILE\models
Invoke-WebRequest -Uri "https://huggingface.co/bartowski/Qwen2.5-Coder-7B-Instruct-GGUF/resolve/main/Qwen2.5-Coder-7B-Instruct-Q6_K.gguf" -OutFile "Qwen2.5-Coder-7B-Instruct-Q6_K.gguf"
dir *.gguf
```

[↑ Sommaire](#sommaire)

---

## 6. Test 1 : `llama-cli` (one-shot)

Dans le dossier `llama.cpp` :

```powershell
.\build\bin\Release\llama-cli.exe `
    -m $env:USERPROFILE\models\Qwen2.5-Coder-7B-Instruct-Q6_K.gguf `
    -p "Écris une fonction Python qui inverse une chaîne." `
    -n 256 `
    -ngl 999
```

> Le caractère `` ` `` (backtick) est la continuation de ligne en PowerShell.

Options :
- `-m` : chemin du `.gguf`
- `-p` : prompt initial
- `-n` : nombre max de tokens à générer
- `-ngl 999` : pousse toutes les couches sur GPU (pour CPU only, retire l'option)

### Mode interactif (chat)

```powershell
.\build\bin\Release\llama-cli.exe `
    -m $env:USERPROFILE\models\Qwen2.5-Coder-7B-Instruct-Q6_K.gguf `
    -cnv `
    -ngl 999
```

Sors avec `Ctrl+C`.

[↑ Sommaire](#sommaire)

---

## 7. Test 2 : `llama-server` (API HTTP compatible OpenAI)

C'est ce mode qui sera utilisé par OpenClaude (tuto 05).

```powershell
.\build\bin\Release\llama-server.exe `
    -m $env:USERPROFILE\models\Qwen2.5-Coder-7B-Instruct-Q6_K.gguf `
    -ngl 999 `
    -c 8192 `
    --port 8080 `
    --host 127.0.0.1
```

Options :
- `-c 8192` : taille du contexte (tokens)
- `--port 8080` : port d'écoute
- `--host 127.0.0.1` : restreint l'accès à localhost (pas exposé sur le réseau)

> **Piège Windows** : utilise `127.0.0.1` partout, jamais `localhost` (résolution IPv6 d'abord).

Dans un autre PowerShell, teste :

```powershell
curl.exe http://127.0.0.1:8080/v1/models

$body = @{ messages = @(@{ role = "user"; content = "dis bonjour" }) } | ConvertTo-Json
Invoke-RestMethod -Uri "http://127.0.0.1:8080/v1/chat/completions" `
    -Method Post -ContentType "application/json" -Body $body
```

L'UI web est dispo sur `http://127.0.0.1:8080` dans ton navigateur.

> **Pare-feu Windows** : la première fois, Windows demandera l'autorisation. Coche « Réseaux privés » (pas publics).

[↑ Sommaire](#sommaire)

---

## 8. Mesurer la performance

```powershell
.\build\bin\Release\llama-bench.exe `
    -m $env:USERPROFILE\models\Qwen2.5-Coder-7B-Instruct-Q6_K.gguf `
    -ngl 999
```

Tu obtiens deux métriques clés :
- **prompt eval** (tok/s) : vitesse de lecture du prompt
- **eval** (tok/s) : vitesse de génération

Comparaison indicative pour Qwen2.5-Coder-7B Q6_K :
- RTX 3060 12 Go → ~80-100 tok/s
- RTX 4090 → ~200+ tok/s
- CPU AVX2 (Ryzen 5600X) → ~5-10 tok/s

[↑ Sommaire](#sommaire)

---

## 9. Mise à jour de llama.cpp

```powershell
cd $env:USERPROFILE\llama.cpp
git pull
cmake --build build --config Release -j 8
```

Si ça casse après une grosse mise à jour : `Remove-Item -Recurse -Force build` puis recommence depuis le `cmake -B build ...`.

[↑ Sommaire](#sommaire)

---

## 10. Cheatsheet

| Action | Commande |
|--------|----------|
| One-shot prompt | `llama-cli.exe -m ... -p "..." -n 256 -ngl 999` |
| Chat interactif | `llama-cli.exe -m ... -cnv -ngl 999` |
| Server HTTP | `llama-server.exe -m ... -ngl 999 -c 8192 --port 8080` |
| Benchmark | `llama-bench.exe -m ... -ngl 999` |
| Quantizer FP16→Q4 | `llama-quantize.exe input.fp16.gguf output.q4_k_m.gguf Q4_K_M` |
| Vérifier la version | `llama-cli.exe --version` |

[↑ Sommaire](#sommaire)

---

## Troubleshooting

| Symptôme | Cause probable | Correctif |
|----------|----------------|-----------|
| `cl : The C++ compiler is not able to compile a simple test program` | PowerShell standard au lieu de Developer PowerShell | Ouvre **« Developer PowerShell for VS 2022 »** depuis le menu Démarrer |
| `nvcc not found` au cmake | CUDA Toolkit pas installé | Installe depuis [developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads) |
| Compilation échoue avec `out of memory` | RAM insuffisante pendant la compilation | Réduis `-j 8` à `-j 2` |
| Tokens très lents avec GPU | `-ngl` non spécifié → tout sur CPU | Toujours mettre `-ngl 999` (offload toutes les couches) |
| `localhost` ne répond pas mais `127.0.0.1` oui | Résolution IPv6 vs IPv4 | Toujours utiliser `127.0.0.1` sur Windows |
| `llama-server` bloqué par pare-feu | Première exécution Windows Defender | Autorise dans la fenêtre qui apparaît, ou Panneau config → Pare-feu |
| Out of memory GPU | Modèle trop gros pour la VRAM | Prends Q4_K_M au lieu de Q6_K, ou mets `-ngl 30` (offload partiel) |
| `unknown model architecture` | GGUF de format trop récent / trop vieux | Met à jour llama.cpp (`git pull` + recompile) |
| `.dll not found` à l'exécution | DLLs CUDA non trouvées | Ajoute `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.x\bin` à ton PATH |

[↑ Sommaire](#sommaire)

---

## Suite

- [`05-[OPENCLAUDE-GRATUIT]-agent-local-llamacpp-avance.md`](./05-[OPENCLAUDE-GRATUIT]-agent-local-llamacpp-avance.md) — brancher OpenClaude sur ton serveur llama.cpp compilé
- [`01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md`](./01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md) — l'alternative simple si compiler te paraît trop
- [Documentation officielle llama.cpp](https://github.com/ggml-org/llama.cpp/tree/master/docs)

[↑ Sommaire](#sommaire)
