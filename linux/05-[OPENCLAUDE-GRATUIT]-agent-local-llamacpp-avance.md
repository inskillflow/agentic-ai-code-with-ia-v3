# 05 — OpenClaude (gratuit, avancé) sur **Linux** : agent branché sur llama.cpp compilé

> **Public** : avancé — tu as déjà compilé llama.cpp.
> **Coût** : 100 % gratuit.
> **OS** : Linux.
> **Durée** : 30 à 45 min.
> **Pré-requis** :
> - llama.cpp compilé (voir [`04-[LLAMACPP-GRATUIT]-...`](./04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md))
> - OpenClaude installé (voir [`02-[OPENCLAUDE-GRATUIT]-...`](./02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md))
> - 1 modèle GGUF téléchargé

> Pour macOS → [`../macos/05-[OPENCLAUDE-GRATUIT]-...`](../macos/05-[OPENCLAUDE-GRATUIT]-agent-local-llamacpp-avance.md).
> Pour Windows → [`../windows/05-[OPENCLAUDE-GRATUIT]-...`](../windows/05-[OPENCLAUDE-GRATUIT]-agent-local-llamacpp-avance.md).

---

## Sommaire

1. [Architecture cible](#1-architecture-cible)
2. [Lancer `llama-server` en mode optimisé](#2-lancer-llama-server-en-mode-optimisé)
3. [Configurer OpenClaude](#3-configurer-openclaude)
4. [Premier test](#4-premier-test)
5. [Choisir un bon modèle pour le tool-calling](#5-choisir-un-bon-modèle-pour-le-tool-calling)
6. [Optimisations clés du serveur](#6-optimisations-clés-du-serveur)
7. [Script de lancement réutilisable](#7-script-de-lancement-réutilisable)
8. [Service systemd (optionnel)](#8-service-systemd-optionnel)
9. [Limites du tool-calling avec un LLM local](#9-limites-du-tool-calling-avec-un-llm-local)
10. [Troubleshooting](#troubleshooting)
11. [Suite](#suite)

---

## 1. Architecture cible

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```
┌──────────────────────┐  HTTP OpenAI-compatible  ┌───────────────────────┐
│      OpenClaude      │ ───────────────────────► │     llama-server      │
│  (CLI agentique)     │                          │  (compilé par toi)    │
│                      │ ◄─────────────────────── │                       │
│  lit/écrit fichiers  │     {choices, tools}     │  GPU CUDA / CPU       │
│  dans ton projet     │                          │  modèle.gguf en RAM   │
└──────────────────────┘                          └───────────────────────┘
```

L'avantage : **aucun service tiers**, tout en local, et tu choisis ton modèle / quantization / paramètres.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 2. Lancer `llama-server` en mode optimisé

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
cd ~/llama.cpp

./build/bin/llama-server \
    -m ~/models/Qwen2.5-Coder-7B-Instruct-Q6_K.gguf \
    --host 127.0.0.1 \
    --port 8080 \
    --ctx-size 16384 \
    -ngl 999 \
    --jinja \
    --chat-template-file models/templates/qwen2.5-coder.jinja
```

> **Important pour OpenClaude** :
> - `--jinja` active le rendu de chat-template Jinja, indispensable au tool-calling.
> - `--chat-template-file` charge le template adapté au modèle (chaque famille de modèles — Qwen, Llama, Mistral — a son propre format de tool-calling).

Tu dois voir dans les logs :
```
main: server is listening on 127.0.0.1:8080 - starting the main loop
```

Laisse ce terminal ouvert.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 3. Configurer OpenClaude

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Dans un **autre** terminal :

```bash
export OPENAI_API_KEY="local"
export OPENAI_BASE_URL="http://127.0.0.1:8080/v1"
export OPENAI_MODEL="qwen2.5-coder-7b"

openclaude
```

> `OPENAI_MODEL` peut être n'importe quel nom — `llama-server` ignore le champ et sert toujours le modèle qu'il a chargé. Mais OpenClaude affiche cette valeur.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 4. Premier test

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Dans OpenClaude :

```
liste les fichiers de mon projet
```

Trois cas possibles :
1. **Idéal** : OpenClaude détecte qu'il doit appeler l'outil `ls` (tool-calling), exécute, te répond avec la liste.
2. **Médiocre** : OpenClaude te propose une commande `ls -la` à exécuter toi-même (mode chat dégradé).
3. **Mauvais** : il invente des noms de fichiers (le modèle hallucine).

Pour passer du cas 3 à 2 ou 1, voir la section suivante sur le choix du modèle.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 5. Choisir un bon modèle pour le tool-calling

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Tous les LLM locaux **ne supportent pas bien** le tool-calling. Les meilleurs en ce moment :

| Modèle | Taille | Note tool-calling | Commande de téléchargement |
|--------|--------|-------------------|----------------------------|
| Qwen2.5-Coder-7B-Instruct | 7B | Bon | [bartowski/Qwen2.5-Coder-7B-Instruct-GGUF](https://huggingface.co/bartowski/Qwen2.5-Coder-7B-Instruct-GGUF) |
| Qwen2.5-Coder-14B-Instruct | 14B | Très bon | [bartowski/Qwen2.5-Coder-14B-Instruct-GGUF](https://huggingface.co/bartowski/Qwen2.5-Coder-14B-Instruct-GGUF) |
| Qwen2.5-Coder-32B-Instruct | 32B | Excellent (proche de Claude Haiku) | [bartowski/Qwen2.5-Coder-32B-Instruct-GGUF](https://huggingface.co/bartowski/Qwen2.5-Coder-32B-Instruct-GGUF) |
| Llama-3.3-70B-Instruct | 70B | Très bon | [bartowski/Llama-3.3-70B-Instruct-GGUF](https://huggingface.co/bartowski/Llama-3.3-70B-Instruct-GGUF) |

> **Règle** : il vaut mieux un Q4_K_M d'un 14B qu'un Q8 d'un 7B pour le tool-calling.

### Templates Jinja correspondants

Les templates sont dans `models/templates/` du dépôt llama.cpp. Si absents :
```bash
ls models/templates/
# Sinon, télécharge-les :
git pull
```

Les noms typiques : `qwen2.5-coder.jinja`, `llama-3.3.jinja`, `mistral.jinja`.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 6. Optimisations clés du serveur

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```bash
./build/bin/llama-server \
    -m ~/models/Qwen2.5-Coder-14B-Instruct-Q4_K_M.gguf \
    --host 127.0.0.1 --port 8080 \
    --ctx-size 16384 \
    --batch-size 512 \
    --ubatch-size 512 \
    -ngl 999 \
    --threads $(nproc) \
    --flash-attn \
    --jinja \
    --chat-template-file models/templates/qwen2.5-coder.jinja
```

| Option | Effet |
|--------|-------|
| `--ctx-size 16384` | Contexte de 16k tokens (utile pour analyser de gros fichiers) |
| `--batch-size 512` | Plus c'est gros, plus le prompt est ingéré vite (consomme VRAM) |
| `-ngl 999` | Toutes les couches sur GPU |
| `--threads $(nproc)` | Threads CPU pour les couches non-GPU |
| `--flash-attn` | Attention optimisée (faster, less VRAM) |
| `--jinja` | Active le tool-calling via template Jinja |

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 7. Script de lancement réutilisable

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Crée `~/bin/llama-openclaude-server.sh` :

```bash
#!/usr/bin/env bash
set -euo pipefail

MODEL="${LLAMA_MODEL:-$HOME/models/Qwen2.5-Coder-7B-Instruct-Q6_K.gguf}"
TEMPLATE="${LLAMA_TEMPLATE:-$HOME/llama.cpp/models/templates/qwen2.5-coder.jinja}"
PORT="${LLAMA_PORT:-8080}"

cd "$HOME/llama.cpp"

exec ./build/bin/llama-server \
    -m "$MODEL" \
    --host 127.0.0.1 \
    --port "$PORT" \
    --ctx-size 16384 \
    --batch-size 512 \
    -ngl 999 \
    --threads "$(nproc)" \
    --flash-attn \
    --jinja \
    --chat-template-file "$TEMPLATE"
```

Rends-le exécutable :
```bash
chmod +x ~/bin/llama-openclaude-server.sh
```

Lance :
```bash
~/bin/llama-openclaude-server.sh
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

## 8. Service systemd (optionnel)

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Pour démarrer automatiquement le serveur au boot :

`~/.config/systemd/user/llama-server.service` :

```ini
[Unit]
Description=llama.cpp server for OpenClaude
After=network-online.target

[Service]
Type=simple
ExecStart=%h/bin/llama-openclaude-server.sh
Restart=on-failure
RestartSec=5

[Install]
WantedBy=default.target
```

Active :
```bash
systemctl --user daemon-reload
systemctl --user enable --now llama-server.service
systemctl --user status llama-server.service
journalctl --user -u llama-server.service -f
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

## 9. Limites du tool-calling avec un LLM local

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Même avec le meilleur modèle local, attends-toi à :

- **moins fiable** que Claude Sonnet (pas magique) ;
- **plus lent** : chaque tool-call = un round-trip + génération ;
- **erreurs de format** : parfois le LLM invente la syntaxe d'appel d'outil.

**Stratégies de mitigation** :
1. Privilégie un modèle ≥ 14B.
2. Utilise un contexte large (`--ctx-size 16384`).
3. Pour les TPs étudiants, **n'attends pas du tool-calling parfait**. Le mode chat (cf. tuto 06) est plus prévisible avec un local 7B.
4. Pour du vrai dev agentique fiable → Claude Code officiel (cf. tuto 08).

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


| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| OpenClaude reçoit du HTML | URL incorrecte | `OPENAI_BASE_URL=http://127.0.0.1:8080/v1` (avec `/v1`) |
| `ECONNREFUSED 8080` | Serveur pas démarré ou crashé | `curl 127.0.0.1:8080/v1/models` ; sinon relance `llama-server` |
| Tool-calling ignoré, modèle répond du texte | `--jinja` ou template manquant | Ajoute `--jinja --chat-template-file models/templates/<modele>.jinja` |
| `Error: model parsing failed` | GGUF corrompu | Re-télécharge le `.gguf` |
| Génération très lente | `-ngl` trop bas, modèle sur CPU | Mets `-ngl 999`, vérifie avec `nvidia-smi` qu'OpenClaude tourne sur GPU |
| OOM CUDA | Modèle trop gros pour la VRAM | Q4 au lieu de Q6, ou `-ngl 30` (offload partiel) |
| OpenClaude affiche `model not found` | Le LLM réponse n'a pas le bon `model` field | C'est juste cosmétique, ignore |

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


- [`06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md`](./06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md) — TP mini-app en mode chat (plus prévisible)
- [`08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md`](./08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md) — TP avec Claude Code officiel pour comparer le mode agent fiable
- [Documentation llama-server](https://github.com/ggml-org/llama.cpp/blob/master/examples/server/README.md)

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>
