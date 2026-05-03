# 04 — llama.cpp (gratuit, avancé) sur **Linux** : compilation et run de modèles GGUF

> **Public** : avancé — tu es à l'aise avec un terminal, `git`, `cmake`.
> **Coût** : 100 % gratuit.
> **OS** : Linux (Ubuntu/Debian/Fedora/Arch).
> **Durée** : 30 à 60 min selon ta machine.
> **Pré-requis** : 8 Go RAM minimum. GPU NVIDIA (CUDA) ou AMD (ROCm) recommandé pour la performance.

> Pour macOS → [`../macos/04-[LLAMACPP-GRATUIT]-...`](../macos/04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md).
> Pour Windows → [`../windows/04-[LLAMACPP-GRATUIT]-...`](../windows/04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md).

> **Pas envie de compiler ?** Reste sur Ollama (tuto 01) qui utilise déjà llama.cpp en interne avec une couche de confort. Ce tuto te donne le **contrôle total** : version, optimisations CPU/GPU spécifiques, modèles exotiques.

---

## Sommaire

1. [Pourquoi compiler llama.cpp soi-même ?](#1-pourquoi-compiler-llamacpp-soi-même-)
2. [Pré-requis selon ton accélérateur](#2-pré-requis-selon-ton-accélérateur)
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
| Choisir précisément l'accélérateur | Mix CUDA/CPU, fallback ROCm |
| Dernière version (release ou main) | Bug fixé d'hier, support nouveau modèle |
| Binaires utilitaires : `llama-quantize`, `llama-bench` | Quantizer toi-même un FP16 → Q4_K_M |
| Server HTTP exposé directement | Branché sur OpenClaude (cf. tuto 05) |

[↑ Sommaire](#sommaire)

---

## 2. Pré-requis selon ton accélérateur

### Communs (toutes distributions)

```bash
# Debian/Ubuntu
sudo apt update
sudo apt install -y build-essential cmake git curl

# Fedora
sudo dnf install -y gcc-c++ cmake git curl

# Arch
sudo pacman -S base-devel cmake git curl
```

Vérifie :
```bash
gcc --version    # ≥ 11 idéal
cmake --version  # ≥ 3.21
```

### Variante GPU NVIDIA (CUDA)

1. Vérifie la présence du GPU :
```bash
nvidia-smi
```
Tu dois voir ton GPU + la version du driver. Si pas de driver : `sudo apt install nvidia-driver-535` (Ubuntu).

2. Installe le **CUDA Toolkit** (qui contient `nvcc`) :
   - Documentation officielle : [developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads).
   - Sur Ubuntu/Debian récent : suit le runfile ou les paquets `nvidia-cuda-toolkit`.
3. Vérifie :
```bash
nvcc --version
```

### Variante GPU AMD (ROCm)

```bash
sudo apt install -y rocm-dev
rocminfo  # liste tes GPU AMD
```

ROCm n'est officiellement supporté que sur certains GPU AMD récents (RX 6000/7000, MI series). Vérifie sur [rocm.docs.amd.com](https://rocm.docs.amd.com/).

### Variante CPU uniquement

Rien de plus à installer. Tu utiliseras les optimisations AVX/AVX2 automatiques.

[↑ Sommaire](#sommaire)

---

## 3. Cloner et compiler

```bash
git clone https://github.com/ggml-org/llama.cpp.git
cd llama.cpp
```

### Build CUDA (NVIDIA)

```bash
cmake -B build -DGGML_CUDA=ON
cmake --build build --config Release -j$(nproc)
```

### Build ROCm (AMD)

```bash
cmake -B build -DGGML_HIPBLAS=ON \
    -DAMDGPU_TARGETS="gfx1030;gfx1100" \
    -DCMAKE_C_COMPILER=hipcc \
    -DCMAKE_CXX_COMPILER=hipcc
cmake --build build --config Release -j$(nproc)
```

> Adapte `gfx1030` à ton GPU. `rocminfo | grep gfx` te donne ta cible.

### Build CPU uniquement

```bash
cmake -B build
cmake --build build --config Release -j$(nproc)
```

### Build Vulkan (alternative GPU généraliste)

```bash
sudo apt install -y libvulkan-dev glslang-tools
cmake -B build -DGGML_VULKAN=ON
cmake --build build --config Release -j$(nproc)
```

La compilation prend 5 à 20 min.

[↑ Sommaire](#sommaire)

---

## 4. Vérifier la compilation

```bash
ls build/bin/llama-*
```

Tu dois voir au moins :
- `llama-cli` — exécution one-shot ou interactive
- `llama-server` — serveur HTTP compatible OpenAI
- `llama-bench` — benchmark
- `llama-quantize` — quantizer un FP16 vers Q4/Q6/Q8

Test rapide :
```bash
./build/bin/llama-cli --version
```

[↑ Sommaire](#sommaire)

---

## 5. Récupérer un modèle GGUF

Va sur HuggingFace, cherche un modèle GGUF (ex. [Qwen2.5-Coder-7B-Instruct-GGUF](https://huggingface.co/bartowski/Qwen2.5-Coder-7B-Instruct-GGUF)).

Choisis une quantization selon ta VRAM :
- 4 Go → `*-Q4_K_M.gguf`
- 8 Go → `*-Q6_K.gguf`
- 12 Go+ → `*-Q8_0.gguf`

Télécharge et place dans `~/models/` :

```bash
mkdir -p ~/models
cd ~/models
curl -L -O https://huggingface.co/bartowski/Qwen2.5-Coder-7B-Instruct-GGUF/resolve/main/Qwen2.5-Coder-7B-Instruct-Q6_K.gguf
ls -lh *.gguf
```

[↑ Sommaire](#sommaire)

---

## 6. Test 1 : `llama-cli` (one-shot)

Dans le dossier `llama.cpp` :

```bash
./build/bin/llama-cli \
    -m ~/models/Qwen2.5-Coder-7B-Instruct-Q6_K.gguf \
    -p "Écris une fonction Python qui inverse une chaîne." \
    -n 256 \
    -ngl 999
```

Options :
- `-m` : chemin du `.gguf`
- `-p` : prompt initial
- `-n` : nombre max de tokens à générer
- `-ngl 999` : pousse toutes les couches sur GPU (pour CPU only, retire l'option)

### Mode interactif (chat)

```bash
./build/bin/llama-cli \
    -m ~/models/Qwen2.5-Coder-7B-Instruct-Q6_K.gguf \
    -cnv \
    -ngl 999
```

Sors avec `Ctrl+C`.

[↑ Sommaire](#sommaire)

---

## 7. Test 2 : `llama-server` (API HTTP compatible OpenAI)

C'est ce mode qui sera utilisé par OpenClaude (tuto 05).

```bash
./build/bin/llama-server \
    -m ~/models/Qwen2.5-Coder-7B-Instruct-Q6_K.gguf \
    -ngl 999 \
    -c 8192 \
    --port 8080 \
    --host 127.0.0.1
```

Options :
- `-c 8192` : taille du contexte (tokens)
- `--port 8080` : port d'écoute
- `--host 127.0.0.1` : restreint l'accès à localhost (pas exposé sur le réseau)

Dans un autre terminal, teste :

```bash
curl http://127.0.0.1:8080/v1/models
curl http://127.0.0.1:8080/v1/chat/completions \
    -H "Content-Type: application/json" \
    -d '{"messages":[{"role":"user","content":"dis bonjour"}]}'
```

L'UI web est dispo sur `http://127.0.0.1:8080` dans ton navigateur.

[↑ Sommaire](#sommaire)

---

## 8. Mesurer la performance

```bash
./build/bin/llama-bench \
    -m ~/models/Qwen2.5-Coder-7B-Instruct-Q6_K.gguf \
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

```bash
cd llama.cpp
git pull
cmake --build build --config Release -j$(nproc)
```

Si ça casse après une grosse mise à jour : `rm -rf build` puis recommence depuis le `cmake -B build ...`.

[↑ Sommaire](#sommaire)

---

## 10. Cheatsheet

| Action | Commande |
|--------|----------|
| One-shot prompt | `llama-cli -m ... -p "..." -n 256 -ngl 999` |
| Chat interactif | `llama-cli -m ... -cnv -ngl 999` |
| Server HTTP | `llama-server -m ... -ngl 999 -c 8192 --port 8080` |
| Benchmark | `llama-bench -m ... -ngl 999` |
| Quantizer FP16→Q4 | `llama-quantize input.fp16.gguf output.q4_k_m.gguf Q4_K_M` |
| Vérifier la version | `llama-cli --version` |

[↑ Sommaire](#sommaire)

---

## Troubleshooting

| Symptôme | Cause probable | Correctif |
|----------|----------------|-----------|
| `nvcc not found` au cmake | CUDA Toolkit pas installé ou pas dans PATH | Installe `cuda-toolkit` puis `export PATH=/usr/local/cuda/bin:$PATH` |
| `Could NOT find HIPBLAS` | ROCm pas installé | `sudo apt install rocm-dev` |
| Compilation échoue avec `signal: killed` | RAM insuffisante pendant la compilation | Réduis `-j$(nproc)` à `-j2` |
| `error: GGML_CUDA_ENABLE_UNIFIED_MEMORY` | Driver NVIDIA trop vieux | Mets à jour le driver : `sudo apt install nvidia-driver-535` |
| Tokens très lents avec GPU | `-ngl` non spécifié → tout sur CPU | Toujours mettre `-ngl 999` (offload toutes les couches) |
| `llama-server` ECONNREFUSED depuis curl | `--host 0.0.0.0` ou firewall | Vérifie `--host 127.0.0.1` et le port |
| Out of memory GPU | Modèle trop gros pour la VRAM | Prends Q4_K_M au lieu de Q6_K, ou mets `-ngl 30` (offload partiel) |
| `unknown model architecture` | GGUF de format trop récent / trop vieux | Met à jour llama.cpp (`git pull` + recompile) |

[↑ Sommaire](#sommaire)

---

## Suite

- [`05-[OPENCLAUDE-GRATUIT]-agent-local-llamacpp-avance.md`](./05-[OPENCLAUDE-GRATUIT]-agent-local-llamacpp-avance.md) — brancher OpenClaude sur ton serveur llama.cpp compilé
- [`01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md`](./01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md) — l'alternative simple si compiler te paraît trop
- [Documentation officielle llama.cpp](https://github.com/ggml-org/llama.cpp/tree/master/docs)

[↑ Sommaire](#sommaire)
