# 04 — llama.cpp (gratuit, avancé) sur **macOS** : compilation et run de modèles GGUF

> **Public** : avancé — tu es à l'aise avec un terminal, `git`, `cmake`.
> **Coût** : 100 % gratuit.
> **OS** : macOS (Apple Silicon recommandé pour Metal).
> **Durée** : 30 à 60 min selon ta machine.
> **Pré-requis** : 8 Go RAM minimum, Apple Silicon (M1/M2/M3) idéal pour la performance Metal.

> Pour Linux → [`../linux/04-[LLAMACPP-GRATUIT]-...`](../linux/04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md).
> Pour Windows → [`../windows/04-[LLAMACPP-GRATUIT]-...`](../windows/04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md).

> **Pas envie de compiler ?** Reste sur Ollama (tuto 01) qui utilise déjà llama.cpp en interne avec une couche de confort. Ce tuto te donne le **contrôle total** : version, optimisations Metal, modèles exotiques.

---

## Sommaire

1. [Pourquoi compiler llama.cpp soi-même ?](#1-pourquoi-compiler-llamacpp-soi-même-)
2. [Pré-requis](#2-pré-requis)
3. [Cloner et compiler avec Metal](#3-cloner-et-compiler-avec-metal)
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

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Avantage | Quand c'est utile |
|----------|-------------------|
| Optimisations Metal explicitement contrôlées | Squeeze max de perf sur Apple Silicon |
| Dernière version (release ou main) | Bug fixé d'hier, support nouveau modèle |
| Binaires utilitaires : `llama-quantize`, `llama-bench` | Quantizer toi-même un FP16 → Q4_K_M |
| Server HTTP exposé directement | Branché sur OpenClaude (cf. tuto 05) |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 2. Pré-requis

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Outils de base

```bash
# Xcode Command Line Tools (clang, make, etc.)
xcode-select --install

# Homebrew (si tu ne l'as pas)
# Voir https://brew.sh

brew install cmake git curl
```

Vérifie :
```bash
clang --version
cmake --version  # ≥ 3.21
git --version
```

### Apple Silicon vs Intel

- **Apple Silicon (M1/M2/M3)** : Metal sera utilisé automatiquement, pas de prérequis supplémentaires.
- **Mac Intel** : pas de Metal performant. Tu utiliseras le CPU (AVX2). Performances modestes.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 3. Cloner et compiler avec Metal

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
git clone https://github.com/ggml-org/llama.cpp.git
cd llama.cpp
```

### Build avec Metal (Apple Silicon, défaut activé)

```bash
cmake -B build
cmake --build build --config Release -j$(sysctl -n hw.ncpu)
```

Sur Apple Silicon, le support Metal est **activé par défaut** depuis le `CMakeLists.txt`. Pas besoin de flag spécifique. Vérifie en lisant la sortie : tu dois voir `-- Metal framework found`.

### Build CPU only (Mac Intel ou si tu veux désactiver Metal)

```bash
cmake -B build -DGGML_METAL=OFF
cmake --build build --config Release -j$(sysctl -n hw.ncpu)
```

La compilation prend 5 à 15 min sur un Mac récent.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 4. Vérifier la compilation

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


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

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 5. Récupérer un modèle GGUF

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Va sur HuggingFace, cherche un modèle GGUF (ex. [Qwen2.5-Coder-7B-Instruct-GGUF](https://huggingface.co/bartowski/Qwen2.5-Coder-7B-Instruct-GGUF)).

Choisis une quantization selon ta RAM unifiée :
- 8 Go → `*-Q4_K_M.gguf`
- 16 Go → `*-Q6_K.gguf`
- 24 Go+ → `*-Q8_0.gguf`

Télécharge et place dans `~/models/` :

```bash
mkdir -p ~/models
cd ~/models
curl -L -O https://huggingface.co/bartowski/Qwen2.5-Coder-7B-Instruct-GGUF/resolve/main/Qwen2.5-Coder-7B-Instruct-Q6_K.gguf
ls -lh *.gguf
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

## 6. Test 1 : `llama-cli` (one-shot)

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


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
- `-ngl 999` : pousse toutes les couches sur GPU/Metal (sur Mac Intel, ignore-le)

### Mode interactif (chat)

```bash
./build/bin/llama-cli \
    -m ~/models/Qwen2.5-Coder-7B-Instruct-Q6_K.gguf \
    -cnv \
    -ngl 999
```

Sors avec `Ctrl+C`.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 7. Test 2 : `llama-server` (API HTTP compatible OpenAI)

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


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

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 8. Mesurer la performance

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
./build/bin/llama-bench \
    -m ~/models/Qwen2.5-Coder-7B-Instruct-Q6_K.gguf \
    -ngl 999
```

Tu obtiens deux métriques clés :
- **prompt eval** (tok/s) : vitesse de lecture du prompt
- **eval** (tok/s) : vitesse de génération

Comparaison indicative pour Qwen2.5-Coder-7B Q6_K :
- M1 16 Go → ~25-35 tok/s
- M2 Pro 32 Go → ~40-55 tok/s
- M3 Max 64 Go → ~70-90 tok/s
- Mac Intel sans Metal → ~3-7 tok/s

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 9. Mise à jour de llama.cpp

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
cd llama.cpp
git pull
cmake --build build --config Release -j$(sysctl -n hw.ncpu)
```

Si ça casse après une grosse mise à jour : `rm -rf build` puis recommence depuis le `cmake -B build ...`.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 10. Cheatsheet

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


| Action | Commande |
|--------|----------|
| One-shot prompt | `llama-cli -m ... -p "..." -n 256 -ngl 999` |
| Chat interactif | `llama-cli -m ... -cnv -ngl 999` |
| Server HTTP | `llama-server -m ... -ngl 999 -c 8192 --port 8080` |
| Benchmark | `llama-bench -m ... -ngl 999` |
| Quantizer FP16→Q4 | `llama-quantize input.fp16.gguf output.q4_k_m.gguf Q4_K_M` |
| Vérifier la version | `llama-cli --version` |

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


| Symptôme | Cause probable | Correctif |
|----------|----------------|-----------|
| `xcrun: error: invalid active developer path` | Xcode Command Line Tools manquants | `xcode-select --install` |
| `command not found: cmake` | Pas installé via Homebrew | `brew install cmake` |
| Compilation échoue avec `signal: killed` | RAM insuffisante pendant la compilation | Réduis `-j$(sysctl -n hw.ncpu)` à `-j2` |
| Pas de mention `Metal` au cmake | Mac Intel | Normal, Metal n'est pas supporté |
| Très lent malgré Apple Silicon | `-ngl` non spécifié → tout sur CPU | Toujours mettre `-ngl 999` (offload toutes les couches Metal) |
| `llama-server` ECONNREFUSED depuis curl | `--host 0.0.0.0` ou pare-feu macOS | Vérifie `--host 127.0.0.1` et autorise dans Réglages → Confidentialité → Pare-feu |
| Out of memory | Modèle trop gros pour la RAM unifiée | Prends Q4_K_M au lieu de Q6_K, ou ferme d'autres apps |
| `unknown model architecture` | GGUF de format trop récent / trop vieux | Met à jour llama.cpp (`git pull` + recompile) |
| Application bloquée par Gatekeeper | Binaire non signé | `xattr -d com.apple.quarantine ./build/bin/llama-cli` |

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


- [`05-[OPENCLAUDE-GRATUIT]-agent-local-llamacpp-avance.md`](./05-[OPENCLAUDE-GRATUIT]-agent-local-llamacpp-avance.md) — brancher OpenClaude sur ton serveur llama.cpp compilé
- [`01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md`](./01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md) — l'alternative simple si compiler te paraît trop
- [Documentation officielle llama.cpp](https://github.com/ggml-org/llama.cpp/tree/master/docs)

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>
