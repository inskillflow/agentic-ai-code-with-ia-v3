# 01 — Installer OpenClaude (Claude gratuit) sur Windows / macOS / Linux

Guide ultra-détaillé pour installer **OpenClaude** (un fork open-source gratuit de Claude Code) avec un backend LLM local — au choix **LM Studio** (interface graphique) ou **Ollama** (ligne de commande). Couvre **Windows**, **macOS** et **Linux**.

---

## Sommaire

- [1. C'est quoi OpenClaude ?](#1-cest-quoi-openclaude-)
- [2. Architecture — comment tout s'imbrique](#2-architecture--comment-tout-simbrique)
- [3. Pré-requis (tous OS)](#3-pré-requis-tous-os)
- [4. Méthode A — LM Studio (interface graphique)](#4-méthode-a--lm-studio-interface-graphique)
- [5. Méthode B — Ollama (ligne de commande)](#5-méthode-b--ollama-ligne-de-commande)
- [6. Comment exporter les variables d'environnement](#6-comment-exporter-les-variables-denvironnement)
- [7. Tester OpenClaude](#7-tester-openclaude)
- [8. Choix du modèle recommandé](#8-choix-du-modèle-recommandé)
- [9. Configuration globale OpenClaude](#9-configuration-globale-openclaude)
- [10. Troubleshooting](#10-troubleshooting)

---

## 1. C'est quoi OpenClaude ?

**OpenClaude** est un fork open-source de **Claude Code** (le CLI agentique d'Anthropic). C'est un **agent terminal** capable de :

- Discuter avec toi en langage naturel
- **Lire et modifier** des fichiers de ton projet
- **Exécuter des commandes shell** (avec ta permission)
- Naviguer dans ton repo, comprendre son architecture
- Coder, refactorer, débugger

La grosse différence avec le vrai Claude Code : **OpenClaude accepte n'importe quel backend LLM compatible avec l'API OpenAI**. Donc tu peux le brancher sur :

- **Ollama** (modèles locaux gratuits, type Gemma, Llama, Qwen)
- **LM Studio** (idem mais avec interface graphique)
- **llama.cpp server** (le plus avancé)
- N'importe quelle API OpenAI-compatible (vLLM, Together, Groq, etc.)

**Résultat** : tu as Claude Code, **gratuit, offline, sur ton GPU**.

### Site officiel et package

- npm : [`@gitlawb/openclaude`](https://www.npmjs.com/package/@gitlawb/openclaude)
- GitHub : voir le lien `repository` sur la page npm

[↑ Sommaire](#sommaire)

---

## 2. Architecture — comment tout s'imbrique

```
┌────────────┐         ┌───────────────────┐         ┌────────────────────┐
│   Toi      │ prompts │    OpenClaude     │   API   │   Backend LLM      │
│            │ ──────> │   (Node.js CLI)   │ ──────> │   - Ollama         │
│   (term.)  │         │                   │ OpenAI- │   - LM Studio      │
│            │ <────── │                   │ compat  │   - llama.cpp      │
│            │ réponse │                   │         │                    │
└────────────┘         └─────────┬─────────┘         └─────────┬──────────┘
                                 │                             │
                                 │                             │  charge
                                 │                             ▼
                                 │                       ┌────────────┐
                                 │                       │  Modèle    │
                                 │                       │  GGUF en   │
                                 │                       │  VRAM/RAM  │
                                 │                       └────────────┘
                                 │
                                 ├─ lit fichiers
                                 ├─ écrit fichiers
                                 ├─ exécute commandes shell (avec confirmation)
                                 └─ navigue dans le repo
```

Les 3 briques :

1. **OpenClaude** — la couche agentique qui te parle, gère les outils et orchestre
2. **Le backend LLM** (Ollama ou LM Studio) — le serveur HTTP qui sert le modèle via API
3. **Le modèle GGUF** — le « cerveau » chargé en VRAM (Gemma, Qwen, Llama...)

OpenClaude communique avec le backend via HTTP sur `localhost`. Aucun appel cloud.

[↑ Sommaire](#sommaire)

---

## 3. Pré-requis (tous OS)

### 3.1 Node.js 18+ minimum

OpenClaude est un package npm. Il te faut Node.js.

#### Vérification

```bash
node --version
npm --version
```

Tu dois voir au moins `v18.x.x` pour Node et `9.x.x` pour npm.

#### Installation

| OS | Méthode recommandée | Commande |
|----|---------------------|----------|
| **Windows** | Installeur officiel | Télécharger sur [nodejs.org](https://nodejs.org/) — version LTS |
| **Windows (winget)** | Via gestionnaire | `winget install OpenJS.NodeJS.LTS` |
| **macOS** | Homebrew | `brew install node` |
| **macOS** | Installeur officiel | [nodejs.org](https://nodejs.org/) |
| **Linux Ubuntu/Debian** | nodesource | `curl -fsSL https://deb.nodesource.com/setup_lts.x \| sudo -E bash - && sudo apt install -y nodejs` |
| **Linux Fedora** | dnf | `sudo dnf install nodejs npm` |
| **Linux Arch** | pacman | `sudo pacman -S nodejs npm` |

Après installation, **ferme et rouvre ton terminal** pour que `node` soit dans le PATH.

### 3.2 Un backend LLM (au choix)

Tu vas choisir **soit** LM Studio **soit** Ollama. Ce ne sont pas concurrents techniquement (ils peuvent cohabiter), juste deux interfaces différentes pour le même besoin.

| Critère | LM Studio | Ollama |
|---------|-----------|--------|
| Interface | GUI visuelle, claire | CLI uniquement |
| Téléchargement modèles | Recherche graphique | `ollama pull <nom>` |
| Modèles HuggingFace customs | Oui, via UI | Oui, via Modelfile (cf. doc 00) |
| Empreinte mémoire | Moyenne | Légère |
| Démarrage automatique | Manuel | Service en arrière-plan |
| Apprentissage | Très facile (clics) | Demande la CLI |
| Performance | Identique | Identique |
| Recommandé pour | Débutants, démos | Power users, scripts, automatisation |

**Conseil** : si c'est ta première fois, prends **LM Studio**. Si tu es à l'aise en terminal et veux scripter, prends **Ollama**.

[↑ Sommaire](#sommaire)

---

## 4. Méthode A — LM Studio (interface graphique)

### 4.1 Installation de LM Studio

#### Windows

1. Va sur [lmstudio.ai](https://lmstudio.ai/)
2. Clique **Download for Windows**
3. Lance le `.exe` téléchargé (ex: `LM-Studio-0.3.x-Setup.exe`)
4. Installation classique en quelques clics
5. Lance LM Studio depuis le menu Démarrer

#### macOS

1. Va sur [lmstudio.ai](https://lmstudio.ai/)
2. Clique **Download for Mac** (M1/M2/M3 ou Intel selon ta machine)
3. Ouvre le `.dmg`
4. Glisse l'app dans `Applications`
5. Lance LM Studio depuis Launchpad ou Spotlight

Sur le premier lancement, macOS peut bloquer l'app (Gatekeeper) :
- Va dans `Réglages Système → Confidentialité et sécurité`
- Tout en bas, clique **Ouvrir quand même**

#### Linux

1. Va sur [lmstudio.ai](https://lmstudio.ai/)
2. Clique **Download for Linux** — tu obtiens un AppImage
3. Rends-le exécutable et lance-le :

```bash
cd ~/Downloads
chmod +x LM-Studio-*.AppImage
./LM-Studio-*.AppImage
```

Pour avoir une icône dans tes applications :

```bash
mkdir -p ~/.local/bin ~/.local/share/applications
mv LM-Studio-*.AppImage ~/.local/bin/lmstudio.AppImage
chmod +x ~/.local/bin/lmstudio.AppImage
```

### 4.2 Télécharger un modèle dans LM Studio

1. Au premier lancement, LM Studio te propose de télécharger un modèle. Tu peux passer cette étape, on va le faire à la main.
2. Clique sur l'icône **loupe** (Search) à gauche de l'écran
3. Dans la barre de recherche, tape `qwen2.5` (recommandé pour démarrer — bon équilibre qualité/taille)
4. Tu vois plusieurs résultats. Choisis par exemple `qwen2.5-7b-instruct`
5. Sur la droite, plusieurs **quantizations** sont disponibles (Q4_K_M, Q6_K, Q8_0...). Pour comprendre laquelle choisir, lis le [doc 00 — section quantizations](../00-installation-windows-ollama-gguf.md#comprendre-les-quantizations-q4-q6-q8-k_m-k_p-avant-de-télécharger).

| Ton matériel | Recommandation |
|--------------|----------------|
| 4 Go VRAM ou CPU only | `Q4_K_M` (~4,4 Go) |
| 6-8 Go VRAM | `Q5_K_M` ou `Q6_K` (~5-6 Go) |
| 10 Go+ VRAM | `Q8_0` (~8 Go) |

6. Clique **Download** sur la version choisie. Le téléchargement prend 5-15 min selon ta connexion.

### 4.3 Démarrer le serveur local LM Studio

1. Clique sur l'icône **Local Server** (la double flèche `>_`) à gauche
2. En haut, dans **Select a model to load**, choisis le modèle que tu viens de télécharger
3. Patiente quelques secondes — le modèle se charge en VRAM (tu vois la barre de progression)
4. **Important** : note le port utilisé, par défaut **`1234`**
5. **Important aussi** : note le nom exact du modèle qui s'affiche en haut, par exemple `qwen2.5-7b-instruct`
6. Clique **Start Server**

Tu vois maintenant un log à droite avec quelque chose comme :

```
[2026-05-02 12:30:12] [INFO] Model loaded: qwen2.5-7b-instruct
[2026-05-02 12:30:12] [INFO] Server running on http://localhost:1234
```

### 4.4 Vérifier que le serveur répond

Dans un terminal séparé :

```bash
# Windows PowerShell, macOS, Linux
curl http://localhost:1234/v1/models
```

Tu dois recevoir un JSON listant ton modèle. Si oui → ton serveur fonctionne.

### 4.5 Installer OpenClaude

Dans un terminal :

```bash
npm install -g @gitlawb/openclaude
```

Sur **macOS/Linux**, si `npm install -g` te dit `EACCES` (permission denied), deux options :

```bash
# Option 1 : utiliser sudo (rapide mais pas idéal)
sudo npm install -g @gitlawb/openclaude

# Option 2 : configurer npm pour installer dans ton home (propre)
mkdir -p ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc   # ou ~/.zshrc
source ~/.bashrc
npm install -g @gitlawb/openclaude
```

Sur **Windows**, en général `npm install -g` marche directement. Si erreur de permission, ouvre PowerShell **en administrateur** et relance.

Vérifie l'installation :

```bash
openclaude --version
openclaude --help
```

### 4.6 Configurer OpenClaude pour LM Studio

OpenClaude lit 3 variables d'environnement OpenAI-compatibles :

| Variable | Valeur pour LM Studio |
|----------|------------------------|
| `OPENAI_API_KEY` | n'importe quoi (LM Studio ne vérifie pas), ex: `lmstudio` |
| `OPENAI_BASE_URL` | `http://localhost:1234/v1` |
| `OPENAI_MODEL` | nom exact du modèle dans LM Studio, ex: `qwen2.5-7b-instruct` |

Comment les exporter selon ton OS → voir [section 6](#6-comment-exporter-les-variables-denvironnement).

Exemple rapide :

```powershell
# Windows PowerShell
$env:OPENAI_API_KEY = "lmstudio"
$env:OPENAI_BASE_URL = "http://localhost:1234/v1"
$env:OPENAI_MODEL = "qwen2.5-7b-instruct"
```

```bash
# macOS / Linux (bash, zsh)
export OPENAI_API_KEY=lmstudio
export OPENAI_BASE_URL=http://localhost:1234/v1
export OPENAI_MODEL=qwen2.5-7b-instruct
```

### 4.7 Lancer OpenClaude

Toujours dans le **même terminal** (où tu as exporté les variables) :

```bash
openclaude
```

OpenClaude démarre, tu vois un prompt interactif. Direction la [section 7](#7-tester-openclaude) pour les premiers tests.

[↑ Sommaire](#sommaire)

---

## 5. Méthode B — Ollama (ligne de commande)

### 5.1 Installation d'Ollama

#### Windows

1. Va sur [ollama.com/download](https://ollama.com/download)
2. Clique **Download for Windows**
3. Lance `OllamaSetup.exe`
4. Installation silencieuse, Ollama démarre en arrière-plan (icône llama dans la barre des tâches)
5. Vérifie dans un terminal :

```powershell
ollama --version
```

#### macOS

```bash
# Méthode 1 : Homebrew (recommandé)
brew install ollama

# Méthode 2 : installeur officiel
# Télécharger https://ollama.com/download/mac
# Glisser dans Applications, lancer une fois
```

Vérifie :

```bash
ollama --version
```

#### Linux

Une seule commande :

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Le script installe le binaire et configure un service systemd. Si tu n'es pas root, il te demandera ton mot de passe sudo.

Vérifie :

```bash
ollama --version
systemctl status ollama   # le service doit être "active (running)"
```

### 5.2 Pull et tester un modèle

```bash
# Télécharge qwen2.5 7B (~4,5 Go)
ollama pull qwen2.5:7b

# Test rapide en chat
ollama run qwen2.5:7b
```

Tu te retrouves dans un chat. Tape `dis bonjour` pour tester. Sors avec `/bye`.

#### Modèles recommandés selon ta machine

```bash
# Petit (4 Go RAM/VRAM)
ollama pull gemma2:2b
ollama pull qwen2.5:3b

# Moyen (8 Go)
ollama pull qwen2.5:7b           # excellent par défaut
ollama pull llama3.1:8b
ollama pull mistral:7b

# Gros (16 Go+)
ollama pull qwen2.5:14b
ollama pull mistral-nemo:12b

# Spécialisé code
ollama pull qwen2.5-coder:7b     # idéal pour OpenClaude (orienté dev)
ollama pull deepseek-coder-v2:16b
```

Pour utiliser un GGUF custom de Hugging Face (Gemma uncensored par exemple), suis le guide [`00-installation-windows-ollama-gguf.md`](../00-installation-windows-ollama-gguf.md).

### 5.3 Vérifier que le serveur Ollama tourne

Ollama lance automatiquement un serveur HTTP sur `127.0.0.1:11434` au démarrage de l'app/service.

```bash
# Windows / macOS / Linux
curl http://127.0.0.1:11434/api/tags
```

Tu reçois un JSON listant tes modèles installés. Si erreur de connexion → relance Ollama :

```bash
# Windows : relance l'app depuis le menu Démarrer
# macOS / Linux : ollama serve
ollama serve
```

### 5.4 Installer OpenClaude

Identique à la méthode A :

```bash
npm install -g @gitlawb/openclaude
```

(Voir [section 4.5](#45-installer-openclaude) si tu as un problème de permissions sur Mac/Linux.)

### 5.5 Configurer OpenClaude pour Ollama

| Variable | Valeur pour Ollama |
|----------|--------------------|
| `OPENAI_API_KEY` | n'importe quoi, ex: `ollama` |
| `OPENAI_BASE_URL` | `http://127.0.0.1:11434/v1` |
| `OPENAI_MODEL` | nom exact, ex: `qwen2.5:7b` ou `gemma4-local:latest` |

⚠️ **Piège Windows** : utilise `127.0.0.1` et **PAS** `localhost`. Sous Windows, `localhost` peut résoudre vers `::1` (IPv6) avant `127.0.0.1` (IPv4), et Ollama n'écoute que sur IPv4. C'est documenté dans [`docs/06-troubleshooting-config-globale-openclaude.md`](../06-troubleshooting-config-globale-openclaude.md).

Exemple rapide :

```powershell
# Windows PowerShell
$env:OPENAI_API_KEY = "ollama"
$env:OPENAI_BASE_URL = "http://127.0.0.1:11434/v1"
$env:OPENAI_MODEL = "qwen2.5:7b"
```

```bash
# macOS / Linux
export OPENAI_API_KEY=ollama
export OPENAI_BASE_URL=http://127.0.0.1:11434/v1
export OPENAI_MODEL=qwen2.5:7b
```

### 5.6 Lancer OpenClaude

```bash
openclaude
```

[↑ Sommaire](#sommaire)

---

## 6. Comment exporter les variables d'environnement

### 6.1 Variables temporaires (uniquement pour la session courante)

#### Windows PowerShell

```powershell
$env:OPENAI_API_KEY = "ollama"
$env:OPENAI_BASE_URL = "http://127.0.0.1:11434/v1"
$env:OPENAI_MODEL = "qwen2.5:7b"
```

Vérification :

```powershell
echo $env:OPENAI_BASE_URL
```

#### Windows CMD

```cmd
set OPENAI_API_KEY=ollama
set OPENAI_BASE_URL=http://127.0.0.1:11434/v1
set OPENAI_MODEL=qwen2.5:7b
```

#### macOS / Linux (bash, zsh)

```bash
export OPENAI_API_KEY=ollama
export OPENAI_BASE_URL=http://127.0.0.1:11434/v1
export OPENAI_MODEL=qwen2.5:7b
```

Vérification :

```bash
echo $OPENAI_BASE_URL
```

### 6.2 Variables permanentes (chaque nouveau terminal)

#### Windows PowerShell — profil utilisateur

1. Ouvre ton profil :

```powershell
notepad $PROFILE
```

Si le fichier n'existe pas, PowerShell propose de le créer.

2. Ajoute à la fin :

```powershell
$env:OPENAI_API_KEY = "ollama"
$env:OPENAI_BASE_URL = "http://127.0.0.1:11434/v1"
$env:OPENAI_MODEL = "qwen2.5:7b"
```

3. Sauvegarde, ferme et rouvre PowerShell.

#### Windows — système (toutes sessions)

```powershell
[System.Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "ollama", "User")
[System.Environment]::SetEnvironmentVariable("OPENAI_BASE_URL", "http://127.0.0.1:11434/v1", "User")
[System.Environment]::SetEnvironmentVariable("OPENAI_MODEL", "qwen2.5:7b", "User")
```

Effet immédiat dans les **nouveaux** terminaux ouverts.

#### macOS / Linux — bashrc / zshrc

Ouvre ton fichier shell selon ton shell :

```bash
# Si tu utilises bash (Linux par défaut, macOS ancien)
nano ~/.bashrc

# Si tu utilises zsh (macOS récent par défaut)
nano ~/.zshrc

# Pour savoir quel shell tu utilises
echo $SHELL
```

Ajoute à la fin :

```bash
export OPENAI_API_KEY=ollama
export OPENAI_BASE_URL=http://127.0.0.1:11434/v1
export OPENAI_MODEL=qwen2.5:7b
```

Recharge :

```bash
source ~/.bashrc   # ou ~/.zshrc
```

### 6.3 Per-projet via fichier `.env`

Si tu veux des variables différentes par projet (par exemple un modèle spécialisé code dans un repo, et un général dans un autre), crée un `.env` à la racine du projet et utilise un wrapper.

#### macOS / Linux

```bash
# Dans le projet
cat > .env <<EOF
OPENAI_API_KEY=ollama
OPENAI_BASE_URL=http://127.0.0.1:11434/v1
OPENAI_MODEL=qwen2.5-coder:7b
EOF

# Lancement
set -a; source .env; set +a; openclaude
```

#### Windows PowerShell

```powershell
# Dans le projet
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

[↑ Sommaire](#sommaire)

---

## 7. Tester OpenClaude

Une fois OpenClaude lancé, tu vois un prompt interactif. Voici 4 tests dans l'ordre, du plus simple au plus complet.

### Test 1 — Sanity check : aide

Avant même de lancer le mode interactif, vérifie que la CLI fonctionne :

```bash
openclaude --help
```

Tu dois voir la liste des commandes et options. Si erreur `command not found` → ton install npm a échoué ou n'est pas dans le PATH.

### Test 2 — Premier chat

```bash
openclaude
```

Tu arrives dans le chat. Tape :

```
dis-moi bonjour en 3 phrases
```

Si tu vois une réponse cohérente arriver token par token → **OpenClaude communique correctement avec ton backend LLM**. Si tu as une erreur :

| Erreur | Cause probable | Solution |
|--------|----------------|----------|
| `ECONNREFUSED 127.0.0.1:11434` | Ollama pas lancé | Lance Ollama / `ollama serve` |
| `ECONNREFUSED 127.0.0.1:1234` | Serveur LM Studio pas démarré | Clique "Start Server" dans LM Studio |
| `404 Not Found` ou `model not found` | Mauvais nom dans `OPENAI_MODEL` | Vérifie avec `ollama list` ou le panneau LM Studio |
| `Input must be provided` | TTY pas détecté (rare) | Voir [`docs/04-guide-complet-troubleshooting.md`](../04-guide-complet-troubleshooting.md) |

### Test 3 — Capacité fichiers

Toujours dans le chat OpenClaude, tape :

```
crée un fichier hello.py qui affiche "Hello from local AI"
```

OpenClaude devrait :
1. Te dire qu'il va créer le fichier
2. Demander confirmation (selon configuration)
3. Créer effectivement `hello.py` dans le dossier courant

Vérifie dans un autre terminal :

```bash
# Tous OS
cat hello.py    # ou type sur Windows
```

Tu dois voir le contenu généré par le modèle.

### Test 4 — Capacité commandes shell

```
exécute la commande qui liste tous les fichiers du dossier courant
```

OpenClaude propose une commande (`ls`, `dir`, `Get-ChildItem` selon l'OS) et te demande confirmation pour l'exécuter. Accepte et regarde la sortie.

### Test 5 — Vrai cas d'usage : refactor

Crée un fichier de test :

```python
# test.py
def x(a, b):
    if a > b:
        return a
    else:
        return b
```

Puis dans OpenClaude :

```
refactor le fichier test.py : renomme la fonction en `maximum`, ajoute des type hints, ajoute une docstring en français
```

OpenClaude lit le fichier, propose un diff, te demande confirmation, et applique. C'est ça la magie d'un agent local.

[↑ Sommaire](#sommaire)

---

## 8. Choix du modèle recommandé

### Selon la taille (RAM/VRAM disponible)

| Catégorie | RAM/VRAM | Modèles Ollama recommandés |
|-----------|----------|----------------------------|
| **Tiny** | 4 Go | `gemma2:2b`, `qwen2.5:3b` |
| **Small** | 6-8 Go | `qwen2.5:7b`, `llama3.1:8b`, `mistral:7b` |
| **Medium** | 12-16 Go | `qwen2.5:14b`, `mistral-nemo:12b` |
| **Large** | 24 Go+ | `qwen2.5:32b`, `llama3.1:70b` (quantifié) |

### Selon l'usage

| Usage | Recommandation | Pourquoi |
|-------|----------------|----------|
| Chat général, polyvalent | `qwen2.5:7b` | Excellent rapport qualité/taille, multilingue dont français |
| Code (refacto, debug) | `qwen2.5-coder:7b` ou `:14b` | Spécifiquement entraîné pour le code |
| Raisonnement complexe | `qwen2.5:14b` ou `qwq:32b` | Plus de paramètres = meilleur raisonnement |
| Langues européennes (FR, ES, DE) | `qwen2.5:7b`, `mistral-nemo:12b` | Multilinguisme natif |
| Uncensored / créatif | `gemma4-local` (custom HF) | Voir [`docs/00-...`](../00-installation-windows-ollama-gguf.md) |
| Très petit / embarqué | `gemma2:2b` | Tourne même sur CPU honnête |

### Tableau de performance estimée (génération tok/s)

| Modèle | RTX 4070 Laptop 8 Go | RTX 4090 24 Go | Apple M3 Pro 18 Go | CPU Ryzen 9 |
|--------|----------------------|----------------|--------------------|--------------|
| qwen2.5:3b | 90 tok/s | 150 tok/s | 60 tok/s | 8 tok/s |
| qwen2.5:7b | 50 tok/s | 110 tok/s | 35 tok/s | 4 tok/s |
| qwen2.5:14b | OOM (trop gros) | 60 tok/s | 18 tok/s | 1.5 tok/s |
| qwen2.5:32b | OOM | 30 tok/s | OOM | 0.5 tok/s |

(Valeurs indicatives, varient selon quantization et contexte)

[↑ Sommaire](#sommaire)

---

## 9. Configuration globale OpenClaude

OpenClaude stocke sa config dans :

| OS | Chemin |
|----|--------|
| Windows | `C:\Users\<toi>\.claude.json` |
| macOS | `/Users/<toi>/.claude.json` |
| Linux | `/home/<toi>/.claude.json` |

Cette config **prend le dessus sur les variables d'environnement** une fois enregistrée. C'est un piège classique — si tu changes `OPENAI_MODEL` dans ton shell mais que `.claude.json` contient déjà un autre modèle, OpenClaude utilisera celui du fichier.

### Voir la config actuelle

```bash
# Windows
type "$env:USERPROFILE\.claude.json"

# macOS / Linux
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

### Modifier le modèle par défaut

Édite le fichier dans ton éditeur (Notepad, nano, code...). Modifie `model` et `baseUrl`. Sauvegarde. Relance OpenClaude.

### Profils multiples

Tu peux avoir plusieurs profils (un pour Ollama, un pour LM Studio, un pour un service distant) :

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

Pour basculer, change `activeProvider` ou utilise une commande slash dans OpenClaude (`/agents` selon version).

Pour le détail complet du troubleshooting de la config globale, voir [`docs/06-troubleshooting-config-globale-openclaude.md`](../06-troubleshooting-config-globale-openclaude.md).

[↑ Sommaire](#sommaire)

---

## 10. Troubleshooting

### Erreurs OpenClaude

#### `Cannot find module '@gitlawb/openclaude'`

L'install npm a foiré. Réinstalle :

```bash
npm uninstall -g @gitlawb/openclaude
npm cache clean --force
npm install -g @gitlawb/openclaude
```

#### `ECONNREFUSED 127.0.0.1:11434` (Ollama)

Ollama n'écoute pas. Trois choses à vérifier :

```bash
# 1. Le service tourne ?
# Windows : icône llama dans la barre des tâches ?
# macOS / Linux :
systemctl status ollama   # Linux
ps aux | grep ollama       # macOS

# 2. Si non, démarre-le
ollama serve

# 3. Vérifie que ça écoute
curl http://127.0.0.1:11434/api/tags
```

#### `ECONNREFUSED 127.0.0.1:1234` (LM Studio)

Le serveur LM Studio n'est pas démarré. Va dans LM Studio → onglet **Local Server** → **Start Server**.

#### `404 model not found`

Ton `OPENAI_MODEL` ne correspond à aucun modèle chargé.

```bash
# Liste les modèles Ollama
ollama list

# Pour LM Studio, regarde le nom dans l'UI (en haut du panneau Local Server)
```

Copie le nom **exact** (avec `:tag` pour Ollama, ex: `qwen2.5:7b`) dans `OPENAI_MODEL`.

#### `Input must be provided either through stdin or as a prompt argument`

OpenClaude n'a pas détecté ton TTY (cas où tu le lances depuis un script ou un IDE qui pipe stdin).

Solution : lance-le **directement depuis ton terminal interactif**, pas depuis un script. Si tu veux scripter, passe le prompt en argument :

```bash
openclaude --print "explique-moi git rebase"
```

#### Réponses très lentes ou hallucinations

- Modèle trop gros pour ta VRAM → swap CPU. Solution : prends une quantization plus légère ou un modèle plus petit
- Modèle pas adapté (ex: modèle généraliste pour du code complexe) → essaie `qwen2.5-coder:7b`
- Trop de contexte → réduis via Modelfile ou paramètre

### Erreurs Ollama

| Erreur | Solution |
|--------|----------|
| `Error: pull model manifest: file does not exist` | Tu as essayé de pull un modèle HuggingFace custom. Voir [doc 00](../00-installation-windows-ollama-gguf.md) pour la méthode Modelfile |
| Démarrage très lent | Premier chargement en VRAM : 5-30 s normal. Si plus, vérifie le GPU |
| Pas de GPU détecté | Driver NVIDIA pas installé, ou Ollama installé avant le driver. Réinstalle Ollama |

### Erreurs LM Studio

| Erreur | Solution |
|--------|----------|
| Modèle ne se charge pas | Pas assez de VRAM. Choisis une quantization plus légère |
| Serveur démarre puis crash | Vérifie les logs à droite, souvent OOM. Décharge d'autres apps GPU |
| `Failed to fetch` dans l'UI | Pas de connexion internet (pour télécharger). Vérifie réseau |

### Erreurs Node / npm

| Erreur | Solution |
|--------|----------|
| `node: command not found` | Node pas dans le PATH. Ferme et rouvre le terminal après install |
| `EACCES` à l'install global (Mac/Linux) | Voir [section 4.5](#45-installer-openclaude) — utilise un prefix dans `~/.npm-global` |
| `npm WARN deprecated` au boot | Ignorables (warnings de dépendances) |

### Diagnostic rapide en 3 commandes

```bash
# 1. Backend LLM répond ?
curl http://127.0.0.1:11434/api/tags    # Ollama
curl http://localhost:1234/v1/models    # LM Studio

# 2. OpenClaude installé ?
openclaude --version

# 3. Variables d'env correctes ?
# Windows
echo "$env:OPENAI_BASE_URL / $env:OPENAI_MODEL"
# Mac / Linux
echo "$OPENAI_BASE_URL / $OPENAI_MODEL"
```

Si les 3 répondent OK, OpenClaude doit fonctionner. Sinon, le problème est sur la commande qui a échoué.

[↑ Sommaire](#sommaire)

---

## Suite

- [`02-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md`](./02-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md) — Ollama en détail, GGUF customs
- [`03-[LLAMACPP-GRATUIT]-compilation-gguf-multiplatforme-avance.md`](./03-[LLAMACPP-GRATUIT]-compilation-gguf-multiplatforme-avance.md) — compiler `llama.cpp` à la main
- [`04-[OPENCLAUDE-GRATUIT]-agent-local-avance-llamacpp-compile-soi-meme.md`](./04-[OPENCLAUDE-GRATUIT]-agent-local-avance-llamacpp-compile-soi-meme.md) — agent local avancé OpenClaude + llama.cpp
- [`05-[CLAUDE-PAYANT]-mini-app-gestionnaire-taches-python-tkinter-mode-agent.md`](./05-[CLAUDE-PAYANT]-mini-app-gestionnaire-taches-python-tkinter-mode-agent.md) — TP payant avec Claude officiel
- [`06-[OPENCLAUDE-GRATUIT]-mini-app-gestionnaire-taches-python-tkinter-mode-chat.md`](./06-[OPENCLAUDE-GRATUIT]-mini-app-gestionnaire-taches-python-tkinter-mode-chat.md) — TP gratuit avec OpenClaude (mode chat)
