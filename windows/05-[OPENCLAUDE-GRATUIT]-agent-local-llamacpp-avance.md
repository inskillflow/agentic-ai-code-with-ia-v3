# 05 — OpenClaude (gratuit, avancé) sur **Windows** : agent branché sur llama.cpp compilé

> **Public** : avancé — tu as déjà compilé llama.cpp.
> **Coût** : 100 % gratuit.
> **OS** : Windows 10 / 11.
> **Durée** : 30 à 45 min.
> **Pré-requis** :
> - llama.cpp compilé (voir [`04-[LLAMACPP-GRATUIT]-...`](./04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md))
> - OpenClaude installé (voir [`02-[OPENCLAUDE-GRATUIT]-...`](./02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md))
> - 1 modèle GGUF téléchargé
> **Terminal** : PowerShell 7 recommandé.

> Pour Linux → [`../linux/05-[OPENCLAUDE-GRATUIT]-...`](../linux/05-[OPENCLAUDE-GRATUIT]-agent-local-llamacpp-avance.md).
> Pour macOS → [`../macos/05-[OPENCLAUDE-GRATUIT]-...`](../macos/05-[OPENCLAUDE-GRATUIT]-agent-local-llamacpp-avance.md).

---

## Sommaire

1. [Architecture cible](#1-architecture-cible)
2. [Lancer `llama-server.exe` en mode optimisé](#2-lancer-llama-serverexe-en-mode-optimisé)
3. [Configurer OpenClaude](#3-configurer-openclaude)
4. [Premier test](#4-premier-test)
5. [Choisir un bon modèle pour le tool-calling](#5-choisir-un-bon-modèle-pour-le-tool-calling)
6. [Optimisations clés du serveur](#6-optimisations-clés-du-serveur)
7. [Script de lancement réutilisable](#7-script-de-lancement-réutilisable)
8. [Tâche planifiée Windows (optionnel)](#8-tâche-planifiée-windows-optionnel)
9. [Limites du tool-calling avec un LLM local](#9-limites-du-tool-calling-avec-un-llm-local)
10. [Troubleshooting](#troubleshooting)
11. [Suite](#suite)

---

## 1. Architecture cible

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```
┌──────────────────────┐  HTTP OpenAI-compatible  ┌───────────────────────┐
│      OpenClaude      │ ───────────────────────► │   llama-server.exe    │
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

## 2. Lancer `llama-server.exe` en mode optimisé

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```powershell
cd $env:USERPROFILE\llama.cpp

.\build\bin\Release\llama-server.exe `
    -m $env:USERPROFILE\models\Qwen2.5-Coder-7B-Instruct-Q6_K.gguf `
    --host 127.0.0.1 `
    --port 8080 `
    --ctx-size 16384 `
    -ngl 999 `
    --jinja `
    --chat-template-file models\templates\qwen2.5-coder.jinja
```

> **Important pour OpenClaude** :
> - `--jinja` active le rendu de chat-template Jinja, indispensable au tool-calling.
> - `--chat-template-file` charge le template adapté au modèle (Qwen, Llama, Mistral ont chacun leur format).
> - Sur Windows, **utilise toujours `127.0.0.1`** (jamais `localhost`).

Tu dois voir dans les logs :
```
main: server is listening on 127.0.0.1:8080 - starting the main loop
```

Laisse cette PowerShell ouverte.

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


Dans une **autre** PowerShell :

```powershell
$env:OPENAI_API_KEY = "local"
$env:OPENAI_BASE_URL = "http://127.0.0.1:8080/v1"
$env:OPENAI_MODEL = "qwen2.5-coder-7b"

openclaude
```

> `OPENAI_MODEL` peut être n'importe quel nom — `llama-server` ignore le champ et sert toujours le modèle qu'il a chargé.

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
1. **Idéal** : OpenClaude détecte qu'il doit appeler l'outil `dir` (tool-calling), exécute, te répond avec la liste.
2. **Médiocre** : OpenClaude te propose une commande `dir` à exécuter toi-même (mode chat dégradé).
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

| Modèle | Taille | Note tool-calling | Lien |
|--------|--------|-------------------|------|
| Qwen2.5-Coder-7B-Instruct | 7B | Bon | [bartowski/Qwen2.5-Coder-7B-Instruct-GGUF](https://huggingface.co/bartowski/Qwen2.5-Coder-7B-Instruct-GGUF) |
| Qwen2.5-Coder-14B-Instruct | 14B | Très bon | [bartowski/Qwen2.5-Coder-14B-Instruct-GGUF](https://huggingface.co/bartowski/Qwen2.5-Coder-14B-Instruct-GGUF) |
| Qwen2.5-Coder-32B-Instruct | 32B | Excellent | [bartowski/Qwen2.5-Coder-32B-Instruct-GGUF](https://huggingface.co/bartowski/Qwen2.5-Coder-32B-Instruct-GGUF) |
| Llama-3.3-70B-Instruct | 70B | Très bon | [bartowski/Llama-3.3-70B-Instruct-GGUF](https://huggingface.co/bartowski/Llama-3.3-70B-Instruct-GGUF) |

> **Règle** : il vaut mieux un Q4_K_M d'un 14B qu'un Q8 d'un 7B pour le tool-calling.

### Templates Jinja correspondants

Les templates sont dans `models\templates\` du dépôt llama.cpp.

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


```powershell
.\build\bin\Release\llama-server.exe `
    -m $env:USERPROFILE\models\Qwen2.5-Coder-14B-Instruct-Q4_K_M.gguf `
    --host 127.0.0.1 --port 8080 `
    --ctx-size 16384 `
    --batch-size 512 `
    --ubatch-size 512 `
    -ngl 999 `
    --threads $env:NUMBER_OF_PROCESSORS `
    --flash-attn `
    --jinja `
    --chat-template-file models\templates\qwen2.5-coder.jinja
```

| Option | Effet |
|--------|-------|
| `--ctx-size 16384` | Contexte de 16k tokens (utile pour analyser de gros fichiers) |
| `--batch-size 512` | Plus c'est gros, plus le prompt est ingéré vite (consomme VRAM) |
| `-ngl 999` | Toutes les couches sur GPU |
| `--threads $env:NUMBER_OF_PROCESSORS` | Threads CPU |
| `--flash-attn` | Attention optimisée |
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


Crée `$env:USERPROFILE\bin\llama-openclaude-server.ps1` :

```powershell
$ErrorActionPreference = "Stop"

$Model = if ($env:LLAMA_MODEL) { $env:LLAMA_MODEL } else { "$env:USERPROFILE\models\Qwen2.5-Coder-7B-Instruct-Q6_K.gguf" }
$Template = if ($env:LLAMA_TEMPLATE) { $env:LLAMA_TEMPLATE } else { "$env:USERPROFILE\llama.cpp\models\templates\qwen2.5-coder.jinja" }
$Port = if ($env:LLAMA_PORT) { $env:LLAMA_PORT } else { "8080" }

Set-Location "$env:USERPROFILE\llama.cpp"

& ".\build\bin\Release\llama-server.exe" `
    -m $Model `
    --host 127.0.0.1 `
    --port $Port `
    --ctx-size 16384 `
    --batch-size 512 `
    -ngl 999 `
    --threads $env:NUMBER_OF_PROCESSORS `
    --flash-attn `
    --jinja `
    --chat-template-file $Template
```

Lance :
```powershell
& "$env:USERPROFILE\bin\llama-openclaude-server.ps1"
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

## 8. Tâche planifiée Windows (optionnel)

<details open>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Pour démarrer automatiquement le serveur à l'ouverture de session :

```powershell
$Action = New-ScheduledTaskAction -Execute "pwsh.exe" `
    -Argument "-WindowStyle Hidden -File `"$env:USERPROFILE\bin\llama-openclaude-server.ps1`""

$Trigger = New-ScheduledTaskTrigger -AtLogOn -User $env:USERNAME

$Settings = New-ScheduledTaskSettingsSet -AllowStartIfOnBatteries -DontStopIfGoingOnBatteries

Register-ScheduledTask -TaskName "LlamaServerForOpenClaude" `
    -Action $Action -Trigger $Trigger -Settings $Settings `
    -Description "Démarre llama-server pour OpenClaude à chaque login"
```

Vérifier :
```powershell
Get-ScheduledTask -TaskName "LlamaServerForOpenClaude"
Start-ScheduledTask -TaskName "LlamaServerForOpenClaude"
```

Désactiver :
```powershell
Unregister-ScheduledTask -TaskName "LlamaServerForOpenClaude" -Confirm:$false
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
| `ECONNREFUSED 8080` | Serveur pas démarré ou crashé | `curl.exe 127.0.0.1:8080/v1/models` ; sinon relance |
| `localhost` ne répond pas, `127.0.0.1` oui | Résolution IPv6 vs IPv4 | Toujours `127.0.0.1` sur Windows |
| Tool-calling ignoré, modèle répond du texte | `--jinja` ou template manquant | Ajoute `--jinja --chat-template-file models\templates\<modele>.jinja` |
| `Error: model parsing failed` | GGUF corrompu | Re-télécharge le `.gguf` |
| Génération lente avec GPU | `-ngl` trop bas | Mets `-ngl 999`, vérifie avec `nvidia-smi` |
| OOM CUDA | Modèle trop gros pour la VRAM | Q4 au lieu de Q6, ou `-ngl 30` (offload partiel) |
| `.dll not found` au lancement de `llama-server.exe` | DLLs CUDA absentes du PATH | Ajoute `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.x\bin` au PATH |
| Pare-feu bloque la première exécution | Windows Defender | Autorise « Réseaux privés » dans la fenêtre qui apparaît |

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
