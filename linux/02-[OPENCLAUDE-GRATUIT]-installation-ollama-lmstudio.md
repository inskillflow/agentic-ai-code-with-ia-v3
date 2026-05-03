# 02 — OpenClaude (gratuit) sur **Linux** : installation et branchement Ollama / LM Studio

> **Public** : tous niveaux.
> **Coût** : gratuit (modèle local).
> **OS** : Linux (Ubuntu, Debian, Fedora, Arch).
> **Durée** : 15 à 20 min.
> **Pré-requis** : Ollama installé et fonctionnel — voir [`01-[OLLAMA-GRATUIT]-...`](./01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md).

> Pour macOS → [`../macos/02-[OPENCLAUDE-GRATUIT]-...`](../macos/02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md).
> Pour Windows → [`../windows/02-[OPENCLAUDE-GRATUIT]-...`](../windows/02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md).

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
9. [Configuration globale `~/.claude.json`](#9-configuration-globale-claudejson)
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

```bash
node --version
npm --version
```

Tu dois voir au moins `v18.x.x` pour Node et `9.x.x` pour npm.

### Installation par distribution

#### Debian / Ubuntu (NodeSource — version récente)
```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt install -y nodejs
```

#### Fedora
```bash
sudo dnf install -y nodejs npm
```

#### Arch
```bash
sudo pacman -S nodejs npm
```

Après installation, **ferme et rouvre ton terminal**.

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


```bash
npm install -g @gitlawb/openclaude
```

### Si erreur `EACCES`

C'est un problème de permissions sur `npm install -g`. Deux solutions :

#### Option A — sudo (rapide mais pas idéal)
```bash
sudo npm install -g @gitlawb/openclaude
```

#### Option B — préfixe dans ton home (recommandé)
```bash
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
npm install -g @gitlawb/openclaude
```

(Adapte `~/.bashrc` en `~/.zshrc` si tu utilises zsh.)

### Vérification

```bash
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

```bash
export OPENAI_API_KEY=ollama
export OPENAI_BASE_URL=http://127.0.0.1:11434/v1
export OPENAI_MODEL=qwen2.5:7b
```

| Variable | Valeur |
|----------|--------|
| `OPENAI_API_KEY` | n'importe quoi (Ollama ne vérifie pas), ex. `ollama` |
| `OPENAI_BASE_URL` | `http://127.0.0.1:11434/v1` (port par défaut Ollama) |
| `OPENAI_MODEL` | nom exact d'un modèle installé, vu par `ollama list` |

Vérifie :
```bash
echo $OPENAI_BASE_URL
echo $OPENAI_MODEL
```

Lance OpenClaude :
```bash
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


LM Studio = interface graphique pour LLMs locaux. Plus visuel qu'Ollama, mais Linux est moins bien supporté que sur Windows/Mac.

1. Va sur [lmstudio.ai](https://lmstudio.ai/), clique **Download for Linux** → tu obtiens un AppImage.
2. Rends-le exécutable :

```bash
cd ~/Téléchargements
chmod +x LM-Studio-*.AppImage
./LM-Studio-*.AppImage
```

3. (Facultatif) Pour avoir une icône dans tes applications :

```bash
mkdir -p ~/.local/bin ~/.local/share/applications
mv LM-Studio-*.AppImage ~/.local/bin/lmstudio.AppImage
chmod +x ~/.local/bin/lmstudio.AppImage
```

Dans LM Studio :
1. Onglet **Search** (loupe) → cherche `qwen2.5-7b-instruct` → choisis une quantization → **Download**.
2. Onglet **Local Server** (icône `>_`) → choisis le modèle → **Start Server** (port 1234 par défaut).

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


```bash
export OPENAI_API_KEY=lmstudio
export OPENAI_BASE_URL=http://localhost:1234/v1
export OPENAI_MODEL=qwen2.5-7b-instruct
openclaude
```

> Note : sur Linux, `localhost` fonctionne aussi bien que `127.0.0.1` (contrairement à Windows). Mais par habitude, on garde `127.0.0.1`.

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


Pour ne pas avoir à les retaper à chaque terminal :

### bash (`~/.bashrc`)
```bash
nano ~/.bashrc
# Ajoute à la fin :
export OPENAI_API_KEY=ollama
export OPENAI_BASE_URL=http://127.0.0.1:11434/v1
export OPENAI_MODEL=qwen2.5:7b

# Recharge :
source ~/.bashrc
```

### zsh (`~/.zshrc`)
Même chose mais dans `~/.zshrc`. Pour savoir quel shell tu utilises : `echo $SHELL`.

### Per-projet via fichier `.env`

```bash
cd mon-projet
cat > .env <<EOF
OPENAI_API_KEY=ollama
OPENAI_BASE_URL=http://127.0.0.1:11434/v1
OPENAI_MODEL=qwen2.5-coder:7b
EOF

# Lancement avec ces variables
set -a; source .env; set +a; openclaude
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

Vérifie dans un autre terminal :
```bash
cat hello.py
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

## 9. Configuration globale `~/.claude.json`

<details>
<summary><b>Cliquer pour replier / deplier cette section</b></summary>


OpenClaude stocke aussi sa config dans `~/.claude.json`. Cette config **prend le dessus sur les variables d'environnement** une fois enregistrée.

### Voir la config
```bash
cat ~/.claude.json
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

Pour basculer, change `activeProvider` ou utilise une commande slash dans OpenClaude (`/agents`).

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
| `Cannot find module '@gitlawb/openclaude'` | Install npm cassée | `npm uninstall -g @gitlawb/openclaude && npm cache clean --force && npm install -g @gitlawb/openclaude` |
| `openclaude: command not found` | PATH npm pas rechargé | Ferme/rouvre le terminal ; vérifie `npm config get prefix` |
| `ECONNREFUSED 127.0.0.1:11434` | Ollama pas lancé | `sudo systemctl start ollama` ou `ollama serve` |
| `ECONNREFUSED 127.0.0.1:1234` | LM Studio pas démarré | Va dans LM Studio → Local Server → Start Server |
| `404 model not found` | `OPENAI_MODEL` n'existe pas | `ollama list` puis copie le nom **exact** (avec `:tag`) |
| `Input must be provided either through stdin...` | TTY non détecté | Lance `openclaude` directement dans ton terminal interactif, pas dans un script |
| Réponses très lentes | Modèle trop gros pour ta VRAM | Prends une quantization plus légère ou un modèle plus petit |

### Diagnostic rapide en 3 commandes

```bash
# 1. Backend LLM répond ?
curl http://127.0.0.1:11434/api/tags

# 2. OpenClaude installé ?
openclaude --version

# 3. Variables d'env correctes ?
echo "$OPENAI_BASE_URL / $OPENAI_MODEL"
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


- [`03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md`](./03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md) — alternative cloud gratuite si tu n'as pas de bon GPU
- [`04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md`](./04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md) — alternative avancée à Ollama
- [`06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md`](./06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md) — premier TP avec OpenClaude + Ollama

</details>

<br>

<div align="center">

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# [&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;RETOUR EN HAUT&nbsp;&nbsp;&nbsp;▲&nbsp;&nbsp;&nbsp;](#sommaire)

### ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

</div>

<br>
