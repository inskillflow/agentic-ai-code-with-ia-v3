# 02 — OpenClaude (gratuit) sur **macOS** : installation et branchement Ollama / LM Studio

> **Public** : tous niveaux.
> **Coût** : gratuit (modèle local).
> **OS** : macOS (Apple Silicon ou Intel).
> **Durée** : 15 à 20 min.
> **Pré-requis** : Ollama installé et fonctionnel — voir [`01-[OLLAMA-GRATUIT]-...`](./01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md).

> Pour Linux → [`../linux/02-[OPENCLAUDE-GRATUIT]-...`](../linux/02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md).
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

[↑ Sommaire](#sommaire)

---

## 2. Installer Node.js

OpenClaude est un package npm, il te faut Node.js 18+.

### Vérification

```bash
node --version
npm --version
```

Tu dois voir au moins `v18.x.x` pour Node et `9.x.x` pour npm.

### Installation

#### Méthode A — Homebrew (recommandée)
```bash
brew install node
```

#### Méthode B — Installeur officiel
Télécharge la version LTS sur [nodejs.org](https://nodejs.org/), ouvre le `.pkg`, installation classique.

Après installation, **ferme et rouvre ton terminal**.

[↑ Sommaire](#sommaire)

---

## 3. Installer OpenClaude (npm global)

```bash
npm install -g @gitlawb/openclaude
```

### Si erreur `EACCES`

C'est un problème de permissions. Deux solutions :

#### Option A — sudo (rapide)
```bash
sudo npm install -g @gitlawb/openclaude
```

#### Option B — préfixe dans ton home (recommandé)
```bash
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.zshrc
source ~/.zshrc
npm install -g @gitlawb/openclaude
```

### Vérification

```bash
openclaude --version
openclaude --help
```

[↑ Sommaire](#sommaire)

---

## 4. Configurer pour Ollama (variables d'environnement)

OpenClaude lit 3 variables d'environnement OpenAI-compatibles.

```bash
export OPENAI_API_KEY=ollama
export OPENAI_BASE_URL=http://127.0.0.1:11434/v1
export OPENAI_MODEL=qwen2.5:7b
```

| Variable | Valeur |
|----------|--------|
| `OPENAI_API_KEY` | n'importe quoi (Ollama ne vérifie pas), ex. `ollama` |
| `OPENAI_BASE_URL` | `http://127.0.0.1:11434/v1` |
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

[↑ Sommaire](#sommaire)

---

## 5. Installer LM Studio (alternative à Ollama)

LM Studio = interface graphique pour LLMs locaux. Très bien pour découvrir.

1. Va sur [lmstudio.ai](https://lmstudio.ai/), clique **Download for Mac** (Apple Silicon ou Intel selon ta machine).
2. Ouvre le `.dmg`, glisse `LM Studio.app` dans `Applications`.
3. Lance LM Studio depuis Launchpad.

> **Premier lancement** : macOS peut bloquer l'app (Gatekeeper). Va dans `Réglages Système → Confidentialité et sécurité`, en bas clique **Ouvrir quand même**.

Dans LM Studio :
1. Onglet **Search** (loupe) → cherche `qwen2.5-7b-instruct` → choisis une quantization (Q6_K si M2/M3 16 Go) → **Download**.
2. Onglet **Local Server** (icône `>_`) → choisis le modèle → **Start Server** (port 1234 par défaut).
3. **Note** le nom exact du modèle qui s'affiche en haut, par exemple `qwen2.5-7b-instruct`.

[↑ Sommaire](#sommaire)

---

## 6. Configurer pour LM Studio

```bash
export OPENAI_API_KEY=lmstudio
export OPENAI_BASE_URL=http://localhost:1234/v1
export OPENAI_MODEL=qwen2.5-7b-instruct
openclaude
```

Sur macOS, `localhost` et `127.0.0.1` sont équivalents.

[↑ Sommaire](#sommaire)

---

## 7. Variables d'environnement permanentes

Le shell par défaut de macOS récent est **zsh** (depuis Catalina).

```bash
nano ~/.zshrc
# Ajoute à la fin :
export OPENAI_API_KEY=ollama
export OPENAI_BASE_URL=http://127.0.0.1:11434/v1
export OPENAI_MODEL=qwen2.5:7b

# Recharge :
source ~/.zshrc
```

> Si tu utilises encore bash (anciens Mac), c'est `~/.bash_profile`. Pour vérifier : `echo $SHELL`.

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

[↑ Sommaire](#sommaire)

---

## 8. Premier test d'OpenClaude

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

[↑ Sommaire](#sommaire)

---

## 9. Configuration globale `~/.claude.json`

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

Pour basculer, change `activeProvider` ou utilise `/agents` dans OpenClaude.

[↑ Sommaire](#sommaire)

---

## Troubleshooting

### Erreurs OpenClaude

| Symptôme | Cause | Correctif |
|----------|-------|-----------|
| `Cannot find module '@gitlawb/openclaude'` | Install npm cassée | `npm uninstall -g @gitlawb/openclaude && npm cache clean --force && npm install -g @gitlawb/openclaude` |
| `openclaude: command not found` | PATH npm pas rechargé | `source ~/.zshrc` ou ferme/rouvre le terminal |
| `ECONNREFUSED 127.0.0.1:11434` | Ollama pas lancé | `brew services restart ollama` ou relance Ollama.app |
| `ECONNREFUSED 127.0.0.1:1234` | LM Studio pas démarré | LM Studio → Local Server → Start Server |
| `404 model not found` | `OPENAI_MODEL` n'existe pas | `ollama list` puis copie le nom **exact** (avec `:tag`) |
| `Input must be provided either through stdin...` | TTY non détecté | Lance `openclaude` directement dans Terminal/iTerm, pas dans un script |
| Réponses très lentes (Mac Intel) | Pas d'accélérateur GPU | Sur Intel Mac, Ollama tourne sur CPU. Prends `gemma2:2b` |

### Diagnostic rapide en 3 commandes

```bash
# 1. Backend LLM répond ?
curl http://127.0.0.1:11434/api/tags

# 2. OpenClaude installé ?
openclaude --version

# 3. Variables d'env correctes ?
echo "$OPENAI_BASE_URL / $OPENAI_MODEL"
```

[↑ Sommaire](#sommaire)

---

## Suite

- [`03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md`](./03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md) — alternative cloud gratuite si ton Mac est limité
- [`04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md`](./04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md) — alternative avancée à Ollama (Metal)
- [`06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md`](./06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md) — premier TP avec OpenClaude + Ollama

[↑ Sommaire](#sommaire)
