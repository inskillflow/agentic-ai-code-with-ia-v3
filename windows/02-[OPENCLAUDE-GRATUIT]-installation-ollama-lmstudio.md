# 02 — OpenClaude (gratuit) sur **Windows** : installation et branchement Ollama / LM Studio

> **Public** : tous niveaux.
> **Coût** : gratuit (modèle local).
> **OS** : Windows 10 / 11.
> **Durée** : 15 à 20 min.
> **Pré-requis** : Ollama installé et fonctionnel — voir [`01-[OLLAMA-GRATUIT]-...`](./01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md).
> **Terminal** : PowerShell 7 recommandé (`winget install Microsoft.PowerShell`).

> Pour Linux → [`../linux/02-[OPENCLAUDE-GRATUIT]-...`](../linux/02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md).
> Pour macOS → [`../macos/02-[OPENCLAUDE-GRATUIT]-...`](../macos/02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md).

---

## Sommaire

1. [Qu'est-ce qu'OpenClaude ?](#1-quest-ce-quopenclaude-)
2. [Installer Node.js](#2-installer-nodejs)
3. [Installer OpenClaude (npm global)](#3-installer-openclaude-npm-global)
4. [Configurer pour Ollama (variables d'environnement)](#4-configurer-pour-ollama-variables-denvironnement)
5. [Installer LM Studio (alternative à Ollama)](#5-installer-lm-studio-alternative-à-ollama)
6. [Configurer pour LM Studio](#6-configurer-pour-lm-studio)
7. [Variables d'environnement permanentes](#7-variables-denvironnement-permanentes)
8. [Premier test d'OpenClaude](#8-premier-test-dopenclaude)
9. [Configuration globale `%USERPROFILE%\.claude.json`](#9-configuration-globale-userprofileclaudejson)
10. [Troubleshooting](#troubleshooting)
11. [Suite](#suite)

---

## 1. Qu'est-ce qu'OpenClaude ?

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


**OpenClaude** est un fork open source de **Claude Code** (le CLI agentique d'Anthropic). C'est un **agent terminal** capable de :

- discuter avec toi en langage naturel ;
- **lire et modifier** des fichiers de ton projet ;
- **exécuter des commandes shell** (avec ta permission) ;
- naviguer dans ton repo, comprendre son architecture ;
- coder, refactorer, débugger.

La grosse différence avec le vrai Claude Code : **OpenClaude accepte n'importe quel backend LLM compatible avec l'API OpenAI**. Donc tu peux le brancher sur :

- **Ollama** (modèles locaux gratuits) ← ce qu'on fait ici
- **LM Studio** (alternative GUI)
- **OpenRouter** (modèles cloud gratuits) → voir tuto 03
- **llama.cpp server** (avancé) → voir tutos 04-05

> **Limite** : avec un modèle local, le « tool-calling » qui permet à OpenClaude de modifier des fichiers est **moins fiable** qu'avec Claude officiel. Pour les TPs, on privilégie le **mode chat** (copier-coller).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 2. Installer Node.js

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


OpenClaude est un package npm, il te faut Node.js 18+.

### Vérification

Dans **PowerShell** :
```powershell
node --version
npm --version
```

Tu dois voir au moins `v18.x.x` pour Node et `9.x.x` pour npm.

### Installation

#### Méthode A — winget (recommandée)
```powershell
winget install OpenJS.NodeJS.LTS
```

#### Méthode B — Installeur officiel
Télécharge l'installeur LTS sur [nodejs.org](https://nodejs.org/), exécute-le, accepte les défauts.

Après installation, **ferme et rouvre PowerShell** pour recharger le PATH.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 3. Installer OpenClaude (npm global)

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```powershell
npm install -g @gitlawb/openclaude
```

### Si erreur de permission

Ouvre PowerShell **en administrateur** (clic droit → « Exécuter en tant qu'administrateur ») et relance la commande.

### Vérification

```powershell
openclaude --version
openclaude --help
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

## 4. Configurer pour Ollama (variables d'environnement)

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


OpenClaude lit 3 variables d'environnement OpenAI-compatibles.

```powershell
$env:OPENAI_API_KEY = "ollama"
$env:OPENAI_BASE_URL = "http://127.0.0.1:11434/v1"
$env:OPENAI_MODEL = "qwen2.5:7b"
```

| Variable | Valeur |
|----------|--------|
| `OPENAI_API_KEY` | n'importe quoi (Ollama ne vérifie pas), ex. `ollama` |
| `OPENAI_BASE_URL` | `http://127.0.0.1:11434/v1` (port par défaut Ollama) |
| `OPENAI_MODEL` | nom exact d'un modèle installé, vu par `ollama list` |

> **Piège Windows fréquent** : utilise `127.0.0.1` et **PAS** `localhost`. Sous Windows, `localhost` peut résoudre vers `::1` (IPv6) avant `127.0.0.1` (IPv4), et Ollama n'écoute que sur IPv4.

Vérifie :
```powershell
echo $env:OPENAI_BASE_URL
echo $env:OPENAI_MODEL
```

Lance OpenClaude :
```powershell
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

## 5. Installer LM Studio (alternative à Ollama)

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


LM Studio = interface graphique pour LLMs locaux. Très bien pour découvrir.

1. Va sur [lmstudio.ai](https://lmstudio.ai/), clique **Download for Windows** → tu obtiens `LM-Studio-x.y.z-Setup.exe`.
2. Lance l'`exe`, accepte les défauts.
3. Lance LM Studio depuis le menu Démarrer.

Dans LM Studio :
1. Onglet **Search** (loupe) → cherche `qwen2.5-7b-instruct` → choisis une quantization → **Download**.
2. Onglet **Local Server** (icône `>_`) → choisis le modèle → **Start Server** (port 1234 par défaut).
3. **Note** le nom exact du modèle qui s'affiche en haut, par exemple `qwen2.5-7b-instruct`.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 6. Configurer pour LM Studio

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


```powershell
$env:OPENAI_API_KEY = "lmstudio"
$env:OPENAI_BASE_URL = "http://localhost:1234/v1"
$env:OPENAI_MODEL = "qwen2.5-7b-instruct"
openclaude
```

> Pour LM Studio, `localhost` fonctionne. Le piège IPv6 est spécifique à Ollama.

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 7. Variables d'environnement permanentes

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Trois portées possibles : session, profil utilisateur, système.

### Session uniquement (déjà vu plus haut)
```powershell
$env:OPENAI_API_KEY = "ollama"
# disparaît à la fermeture du terminal
```

### Profil utilisateur PowerShell

Édite ton profil :
```powershell
notepad $PROFILE
```

Si le fichier n'existe pas, PowerShell propose de le créer. Ajoute à la fin :
```powershell
$env:OPENAI_API_KEY = "ollama"
$env:OPENAI_BASE_URL = "http://127.0.0.1:11434/v1"
$env:OPENAI_MODEL = "qwen2.5:7b"
```

Sauvegarde, ferme et rouvre PowerShell.

### Système (toutes sessions, toutes apps)

```powershell
[System.Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "ollama", "User")
[System.Environment]::SetEnvironmentVariable("OPENAI_BASE_URL", "http://127.0.0.1:11434/v1", "User")
[System.Environment]::SetEnvironmentVariable("OPENAI_MODEL", "qwen2.5:7b", "User")
```

Effet immédiat dans les **nouveaux** terminaux ouverts.

### Per-projet via fichier `.env`

```powershell
@"
OPENAI_API_KEY=ollama
OPENAI_BASE_URL=http://127.0.0.1:11434/v1
OPENAI_MODEL=qwen2.5-coder:7b
"@ | Set-Content .env

# Wrapper de lancement
Get-Content .env | ForEach-Object {
    if ($_ -match "^([^=]+)=(.*)$") {
        Set-Item -Path "env:$($matches[1])" -Value $matches[2]
    }
}
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

## 8. Premier test d'OpenClaude

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


Une fois lancé, tu vois un prompt interactif. Tape :

```
dis-moi bonjour en 3 phrases
```

Si tu vois une réponse cohérente → **OpenClaude communique avec Ollama**.

### Test création de fichier

```
crée un fichier hello.py qui affiche "Hello from local AI"
```

Vérifie dans un autre PowerShell :
```powershell
type hello.py
```

> Avec un modèle local, le tool-calling peut échouer. Si OpenClaude te répond du code mais ne crée pas le fichier, copie-le manuellement. C'est le mode chat (cf. tuto 06).

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>

---

## 9. Configuration globale `%USERPROFILE%\.claude.json`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


OpenClaude stocke aussi sa config dans `C:\Users\<toi>\.claude.json`. Cette config **prend le dessus sur les variables d'environnement** une fois enregistrée.

### Voir la config
```powershell
type "$env:USERPROFILE\.claude.json"
```

### Structure typique
```json
{
  "providerProfiles": {
    "default": {
      "baseUrl": "http://127.0.0.1:11434/v1",
      "apiKey": "ollama",
      "model": "qwen2.5:7b"
    }
  },
  "activeProvider": "default"
}
```

### Profils multiples
```json
{
  "providerProfiles": {
    "ollama-fast": {
      "baseUrl": "http://127.0.0.1:11434/v1",
      "apiKey": "ollama",
      "model": "qwen2.5:7b"
    },
    "lmstudio-quality": {
      "baseUrl": "http://localhost:1234/v1",
      "apiKey": "lmstudio",
      "model": "qwen2.5-14b-instruct"
    }
  },
  "activeProvider": "ollama-fast"
}
```

Pour basculer, change `activeProvider` ou utilise `/agents` dans OpenClaude.

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

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


### Erreurs OpenClaude

| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| `Cannot find module '@gitlawb/openclaude'` | Install npm cassée | `npm uninstall -g @gitlawb/openclaude ; npm cache clean --force ; npm install -g @gitlawb/openclaude` |
| `openclaude: command not found` | PATH npm pas rechargé | Ferme/rouvre PowerShell |
| `ECONNREFUSED 127.0.0.1:11434` | Ollama pas lancé | Relance l'app Ollama depuis le menu Démarrer, ou `ollama serve` |
| `localhost` ne fonctionne pas mais `127.0.0.1` oui | Résolution IPv6 vs IPv4 | Toujours utiliser `127.0.0.1` pour Ollama |
| `ECONNREFUSED 127.0.0.1:1234` | LM Studio pas démarré | LM Studio → Local Server → Start Server |
| `404 model not found` | `OPENAI_MODEL` n'existe pas | `ollama list` puis copie le nom **exact** (avec `:tag`) |
| `Input must be provided either through stdin...` | TTY non détecté | Lance `openclaude` directement dans PowerShell, pas dans un script |
| Accents cassés | Console CP1252 | `chcp 65001` avant la commande |
| Erreur d'exécution de scripts PS | ExecutionPolicy restrictive | `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser` (une fois) |

### Diagnostic rapide en 3 commandes

```powershell
# 1. Backend LLM répond ?
curl.exe http://127.0.0.1:11434/api/tags

# 2. OpenClaude installé ?
openclaude --version

# 3. Variables d'env correctes ?
echo "$env:OPENAI_BASE_URL / $env:OPENAI_MODEL"
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

## Suite

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


- [`03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md`](./03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md) — alternative cloud gratuite si ton PC est limité
- [`04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md`](./04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md) — alternative avancée à Ollama (CUDA)
- [`06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md`](./06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md) — premier TP avec OpenClaude + Ollama

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>
