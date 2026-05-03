# 05 — OpenClaude (gratuit, avancé) sur **macOS** : agent branché sur llama.cpp compilé

> **Public** : avancé — tu as déjà compilé llama.cpp.
> **Coût** : 100 % gratuit.
> **OS** : macOS (Apple Silicon recommandé).
> **Durée** : 30 à 45 min.
> **Pré-requis** :
> - llama.cpp compilé (voir [`04-[LLAMACPP-GRATUIT]-...`](./04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md))
> - OpenClaude installé (voir [`02-[OPENCLAUDE-GRATUIT]-...`](./02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md))
> - 1 modèle GGUF téléchargé

> Pour Linux → [`../linux/05-[OPENCLAUDE-GRATUIT]-...`](../linux/05-[OPENCLAUDE-GRATUIT]-agent-local-llamacpp-avance.md).
> Pour Windows → [`../windows/05-[OPENCLAUDE-GRATUIT]-...`](../windows/05-[OPENCLAUDE-GRATUIT]-agent-local-llamacpp-avance.md).

---

## Sommaire

1. [Architecture cible](#1-architecture-cible)
2. [Lancer `llama-server` en mode optimisé](#2-lancer-llama-server-en-mode-optimisé)
3. [Configurer OpenClaude](#3-configurer-openclaude)
4. [Premier test](#4-premier-test)
5. [Choisir un bon modèle pour le tool-calling](#5-choisir-un-bon-modèle-pour-le-tool-calling)
6. [Optimisations clés du serveur (Metal)](#6-optimisations-clés-du-serveur-metal)
7. [Script de lancement réutilisable](#7-script-de-lancement-réutilisable)
8. [Service launchd (optionnel)](#8-service-launchd-optionnel)
9. [Limites du tool-calling avec un LLM local](#9-limites-du-tool-calling-avec-un-llm-local)
10. [Troubleshooting](#troubleshooting)
11. [Suite](#suite)

---

## 1. Architecture cible

```
┌──────────────────────┐  HTTP OpenAI-compatible  ┌───────────────────────┐
│      OpenClaude      │ ───────────────────────► │     llama-server      │
│  (CLI agentique)     │                          │  (compilé par toi)    │
│                      │ ◄─────────────────────── │                       │
│  lit/écrit fichiers  │     {choices, tools}     │  Metal (Apple Silicon)│
│  dans ton projet     │                          │  modèle.gguf en RAM   │
└──────────────────────┘                          └───────────────────────┘
```

L'avantage : **aucun service tiers**, tout en local sur Apple Silicon.

[↑ Sommaire](#sommaire)

---

## 2. Lancer `llama-server` en mode optimisé

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
> - `--chat-template-file` charge le template adapté au modèle (Qwen, Llama, Mistral ont chacun leur format).

Tu dois voir dans les logs :
```
main: server is listening on 127.0.0.1:8080 - starting the main loop
```

Laisse ce terminal ouvert.

[↑ Sommaire](#sommaire)

---

## 3. Configurer OpenClaude

Dans un **autre** terminal :

```bash
export OPENAI_API_KEY="local"
export OPENAI_BASE_URL="http://127.0.0.1:8080/v1"
export OPENAI_MODEL="qwen2.5-coder-7b"

openclaude
```

> `OPENAI_MODEL` peut être n'importe quel nom — `llama-server` ignore le champ et sert toujours le modèle qu'il a chargé.

[↑ Sommaire](#sommaire)

---

## 4. Premier test

Dans OpenClaude :

```
liste les fichiers de mon projet
```

Trois cas possibles :
1. **Idéal** : OpenClaude détecte qu'il doit appeler l'outil `ls` (tool-calling), exécute, te répond avec la liste.
2. **Médiocre** : OpenClaude te propose une commande `ls -la` à exécuter toi-même (mode chat dégradé).
3. **Mauvais** : il invente des noms de fichiers (le modèle hallucine).

Pour passer du cas 3 à 2 ou 1, voir la section suivante sur le choix du modèle.

[↑ Sommaire](#sommaire)

---

## 5. Choisir un bon modèle pour le tool-calling

Tous les LLM locaux **ne supportent pas bien** le tool-calling. Les meilleurs en ce moment :

| Modèle | Taille | Note tool-calling | Lien |
|--------|--------|-------------------|------|
| Qwen2.5-Coder-7B-Instruct | 7B | Bon | [bartowski/Qwen2.5-Coder-7B-Instruct-GGUF](https://huggingface.co/bartowski/Qwen2.5-Coder-7B-Instruct-GGUF) |
| Qwen2.5-Coder-14B-Instruct | 14B | Très bon | [bartowski/Qwen2.5-Coder-14B-Instruct-GGUF](https://huggingface.co/bartowski/Qwen2.5-Coder-14B-Instruct-GGUF) |
| Qwen2.5-Coder-32B-Instruct | 32B | Excellent | [bartowski/Qwen2.5-Coder-32B-Instruct-GGUF](https://huggingface.co/bartowski/Qwen2.5-Coder-32B-Instruct-GGUF) |
| Llama-3.3-70B-Instruct | 70B | Très bon | [bartowski/Llama-3.3-70B-Instruct-GGUF](https://huggingface.co/bartowski/Llama-3.3-70B-Instruct-GGUF) |

> **Règle** : il vaut mieux un Q4_K_M d'un 14B qu'un Q8 d'un 7B pour le tool-calling.

> **Sur Apple Silicon** : la RAM unifiée fait office de VRAM. Un M2 Pro 32 Go peut faire tourner un 32B en Q4_K_M (~20 Go).

### Templates Jinja correspondants

Les templates sont dans `models/templates/` du dépôt llama.cpp.

[↑ Sommaire](#sommaire)

---

## 6. Optimisations clés du serveur (Metal)

```bash
./build/bin/llama-server \
    -m ~/models/Qwen2.5-Coder-14B-Instruct-Q4_K_M.gguf \
    --host 127.0.0.1 --port 8080 \
    --ctx-size 16384 \
    --batch-size 512 \
    --ubatch-size 512 \
    -ngl 999 \
    --threads $(sysctl -n hw.ncpu) \
    --flash-attn \
    --jinja \
    --chat-template-file models/templates/qwen2.5-coder.jinja
```

| Option | Effet sur macOS |
|--------|-----------------|
| `--ctx-size 16384` | Contexte de 16k tokens |
| `--batch-size 512` | Plus c'est gros, plus le prompt est ingéré vite (consomme RAM unifiée) |
| `-ngl 999` | Toutes les couches sur Metal |
| `--threads $(sysctl -n hw.ncpu)` | Threads CPU pour les couches non-GPU |
| `--flash-attn` | Attention optimisée |
| `--jinja` | Active le tool-calling via template Jinja |

[↑ Sommaire](#sommaire)

---

## 7. Script de lancement réutilisable

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
    --threads "$(sysctl -n hw.ncpu)" \
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

[↑ Sommaire](#sommaire)

---

## 8. Service launchd (optionnel)

Pour démarrer automatiquement le serveur à la connexion :

`~/Library/LaunchAgents/com.user.llamaserver.plist` :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.user.llamaserver</string>
    <key>ProgramArguments</key>
    <array>
        <string>/Users/TON_USER/bin/llama-openclaude-server.sh</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
    <key>StandardOutPath</key>
    <string>/tmp/llama-server.log</string>
    <key>StandardErrorPath</key>
    <string>/tmp/llama-server.err</string>
</dict>
</plist>
```

Active :
```bash
launchctl load ~/Library/LaunchAgents/com.user.llamaserver.plist
launchctl start com.user.llamaserver
tail -f /tmp/llama-server.log
```

Désactive :
```bash
launchctl unload ~/Library/LaunchAgents/com.user.llamaserver.plist
```

[↑ Sommaire](#sommaire)

---

## 9. Limites du tool-calling avec un LLM local

Même avec le meilleur modèle local, attends-toi à :

- **moins fiable** que Claude Sonnet (pas magique) ;
- **plus lent** : chaque tool-call = un round-trip + génération ;
- **erreurs de format** : parfois le LLM invente la syntaxe d'appel d'outil.

**Stratégies de mitigation** :
1. Privilégie un modèle ≥ 14B (faisable sur M2 Pro/M3 Pro 32 Go).
2. Utilise un contexte large (`--ctx-size 16384`).
3. Pour les TPs étudiants, **n'attends pas du tool-calling parfait**. Le mode chat (cf. tuto 06) est plus prévisible avec un local 7B.
4. Pour du vrai dev agentique fiable → Claude Code officiel (cf. tuto 08).

[↑ Sommaire](#sommaire)

---

## Troubleshooting

| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| OpenClaude reçoit du HTML | URL incorrecte | `OPENAI_BASE_URL=http://127.0.0.1:8080/v1` (avec `/v1`) |
| `ECONNREFUSED 8080` | Serveur pas démarré ou crashé | `curl 127.0.0.1:8080/v1/models` ; sinon relance `llama-server` |
| Tool-calling ignoré, modèle répond du texte | `--jinja` ou template manquant | Ajoute `--jinja --chat-template-file models/templates/<modele>.jinja` |
| `Error: model parsing failed` | GGUF corrompu | Re-télécharge le `.gguf` |
| Génération lente sur Apple Silicon | `-ngl` trop bas | Mets `-ngl 999`. Métal doit s'afficher dans les logs |
| OOM (mémoire saturée) | Modèle trop gros pour la RAM unifiée | Q4 au lieu de Q6, ou ferme d'autres apps |
| Métal indisponible (Mac Intel) | Pas d'Apple Silicon | Tu tournes en CPU, c'est lent. Prends `gemma2:2b` ou utilise OpenRouter (tuto 03) |
| OpenClaude affiche `model not found` | Cosmétique | Ignore |

[↑ Sommaire](#sommaire)

---

## Suite

- [`06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md`](./06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md) — TP mini-app en mode chat (plus prévisible)
- [`08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md`](./08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md) — TP avec Claude Code officiel pour comparer le mode agent fiable
- [Documentation llama-server](https://github.com/ggml-org/llama.cpp/blob/master/examples/server/README.md)

[↑ Sommaire](#sommaire)
