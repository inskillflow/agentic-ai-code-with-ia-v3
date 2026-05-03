# Claude Code Demos — Tutos IA assistée pour étudiants

Dépôt pédagogique francophone qui couvre **3 façons** de faire de l'IA assistée pour le code :

1. **Local gratuit** (Ollama, llama.cpp, OpenClaude)
2. **Cloud gratuit** (OpenRouter — modèles `:free` Llama 405B / Gemini Flash / Qwen Coder 32B)
3. **Cloud payant officiel** (Claude Code d'Anthropic, mode agent)

Chaque tutoriel existe en **3 versions** : Linux, macOS, Windows. Choisis ton OS et suis les liens.

---

## Si tu es sur **Windows**

Va dans [`windows/`](./windows/)

| # | Tuto |
|---|------|
| 01 | [Ollama — quickstart installation modèles locaux](./windows/01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md) |
| 02 | [OpenClaude — installation Ollama / LM Studio](./windows/02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md) |
| 03 | [OpenRouter — modèles cloud gratuits](./windows/03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md) |
| 04 | [llama.cpp — compilation GGUF avancée](./windows/04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md) |
| 05 | [OpenClaude + llama.cpp avancé](./windows/05-[OPENCLAUDE-GRATUIT]-agent-local-llamacpp-avance.md) |
| 06 | TP — mini-app Tkinter avec OpenClaude + Ollama (mode chat) — [→](./windows/06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md) |
| 07 | TP — mini-app Tkinter avec OpenRouter cloud (mode chat) — [→](./windows/07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md) |
| 08 | TP — mini-app Tkinter avec Claude Code officiel (mode agent, payant) — [→](./windows/08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md) |

## Si tu es sur **macOS**

Va dans [`macos/`](./macos/)

| # | Tuto |
|---|------|
| 01 | [Ollama — quickstart installation modèles locaux](./macos/01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md) |
| 02 | [OpenClaude — installation Ollama / LM Studio](./macos/02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md) |
| 03 | [OpenRouter — modèles cloud gratuits](./macos/03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md) |
| 04 | [llama.cpp — compilation GGUF avancée (Metal)](./macos/04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md) |
| 05 | [OpenClaude + llama.cpp avancé](./macos/05-[OPENCLAUDE-GRATUIT]-agent-local-llamacpp-avance.md) |
| 06 | TP — mini-app Tkinter avec OpenClaude + Ollama (mode chat) — [→](./macos/06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md) |
| 07 | TP — mini-app Tkinter avec OpenRouter cloud (mode chat) — [→](./macos/07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md) |
| 08 | TP — mini-app Tkinter avec Claude Code officiel (mode agent, payant) — [→](./macos/08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md) |

## Si tu es sur **Linux**

Va dans [`linux/`](./linux/)

| # | Tuto |
|---|------|
| 01 | [Ollama — quickstart installation modèles locaux](./linux/01-[OLLAMA-GRATUIT]-quickstart-installation-modeles-locaux.md) |
| 02 | [OpenClaude — installation Ollama / LM Studio](./linux/02-[OPENCLAUDE-GRATUIT]-installation-ollama-lmstudio.md) |
| 03 | [OpenRouter — modèles cloud gratuits](./linux/03-[OPENROUTER-GRATUIT]-openclaude-modeles-cloud-gratuits.md) |
| 04 | [llama.cpp — compilation GGUF avancée (CUDA / ROCm)](./linux/04-[LLAMACPP-GRATUIT]-compilation-gguf-avance.md) |
| 05 | [OpenClaude + llama.cpp avancé](./linux/05-[OPENCLAUDE-GRATUIT]-agent-local-llamacpp-avance.md) |
| 06 | TP — mini-app Tkinter avec OpenClaude + Ollama (mode chat) — [→](./linux/06-[OPENCLAUDE-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-ollama.md) |
| 07 | TP — mini-app Tkinter avec OpenRouter cloud (mode chat) — [→](./linux/07-[OPENROUTER-GRATUIT]-tp-mini-app-python-tkinter-mode-chat-cloud.md) |
| 08 | TP — mini-app Tkinter avec Claude Code officiel (mode agent, payant) — [→](./linux/08-[CLAUDE-PAYANT]-tp-mini-app-python-tkinter-mode-agent.md) |

---

## Conventions de nommage

`NN-[OUTIL-PRIX]-description-significative.md`

| Élément | Exemples |
|---------|----------|
| `NN` | numéro d'ordre pédagogique (01 à 08) |
| `OUTIL` | `OLLAMA`, `OPENCLAUDE`, `OPENROUTER`, `LLAMACPP`, `CLAUDE` |
| `PRIX` | `GRATUIT` ou `PAYANT` (toujours explicite) |

---

## Parcours recommandés

### Parcours « débutant sans budget, sans GPU »
1. Tuto **01** (Ollama) — essaye, si ton CPU est lent, abandonne
2. Tuto **02** (OpenClaude install)
3. Tuto **03** (OpenRouter cloud gratuit) — **ta vraie route si pas de GPU**
4. Tuto **07** (TP avec OpenRouter)

### Parcours « débutant avec un GPU NVIDIA / Apple Silicon »
1. Tuto **01** (Ollama) — bien fluide chez toi
2. Tuto **02** (OpenClaude install + Ollama)
3. Tuto **06** (TP avec Ollama local)
4. Optionnel : Tuto **03** (essayer le cloud gratuit aussi)

### Parcours « pro / découverte de l'agentique »
1. Tuto **01** ou **03** (avoir un LLM accessible)
2. Tuto **02** (OpenClaude install)
3. Tuto **06** ou **07** (TP gratuit pour comprendre)
4. Tuto **08** (Claude Code officiel — voir la différence)

### Parcours « expert »
1. Tuto **04** (compilation llama.cpp avec ton accélérateur)
2. Tuto **05** (OpenClaude branché sur llama.cpp custom)
3. Tuto **08** (comparer avec Claude Code officiel)

---

## Comparatif rapide des outils

| Outil | Prix | Mode | Bon pour | Limite |
|-------|------|------|----------|--------|
| **Ollama** | gratuit | LLM local | Découverte, dev sans internet | Quality dépend du GPU |
| **LM Studio** | gratuit | LLM local (GUI) | Découverte visuelle | Idem Ollama |
| **OpenRouter `:free`** | gratuit | LLM cloud | Sans GPU, modèles 70B-405B | Quotas (~200 req/jour) |
| **OpenRouter (Claude payant)** | payant | LLM cloud | Mix de modèles dans une seule API | Légèrement plus cher que API directe Anthropic |
| **OpenClaude (CLI)** | gratuit | Agent local | Apprendre l'agentique | Tool-calling moyen avec petits modèles |
| **llama.cpp** | gratuit | Backend bas niveau | Optimisations max, modèles exotiques | Compilation requise |
| **Claude Code** | payant | Agent officiel Anthropic | Productivité réelle, agent fiable | ~20$/mois ou facturation API |

---

## Structure des tutoriels

Chaque tuto suit la même architecture :

1. Bandeau en haut : public, coût, OS, durée, pré-requis
2. Liens vers les versions des autres OS
3. **Sommaire** cliquable
4. Sections numérotées avec liens « ↑ Sommaire » sous chacune
5. **Troubleshooting** consolidé en fin de fichier
6. Section **Suite** avec les liens vers les tutos qui font sens après

---

## Pré-requis communs

| Outil | Linux | macOS | Windows |
|-------|-------|-------|---------|
| Node.js 18+ | apt/dnf/pacman | `brew install node` | `winget install OpenJS.NodeJS.LTS` |
| Python 3.9+ avec Tkinter | `apt install python3-tk` | `brew install python-tk` | inclus si « Tcl/Tk » coché à l'install |
| Git | apt/dnf/pacman | inclus (Xcode CLT) | `winget install Git.Git` |
| GPU drivers (optionnel) | `nvidia-driver-535` | inutile (Metal natif) | driver NVIDIA Studio |

---

## Dossier `divers/`

Le dossier [`divers/`](./divers/) contient les versions intermédiaires multiplateformes (un seul fichier par tuto avec les commandes Linux/macOS/Windows entrelacées) qu'on a produites avant d'éclater par OS. Conservées pour référence.

---

## Contribuer

Les tutos évoluent vite avec les nouvelles versions des outils. PRs bienvenues pour :

- mettre à jour les versions de modèles `:free` d'OpenRouter
- ajouter des modèles GGUF performants
- corriger les troubleshootings d'OS spécifiques

---

## Dépôts liés (écosystème inskillflow)

Ce dépôt fait partie d'une série progressive sur l'IA assistée pour le code. Voici les autres briques utiles :

### [agentic-ai-code-with-ia-v1](https://github.com/inskillflow/agentic-ai-code-with-ia-v1)

**Première version pédagogique — pourquoi local ≠ agentique.**
Point d'entrée historique de la série. Explique en détail la différence entre :

- un LLM **local** qu'on consulte (chat, copier-coller dans l'éditeur)
- un agent **agentique** qui lit/écrit/exécute du code dans le projet

Inclut la grosse section **troubleshooting d'origine** (drivers GPU, ports, modèles trop gros pour la VRAM, erreurs `ECONNREFUSED`, problèmes de PATH, etc.) qu'on a ensuite répartie par tuto dans v3.

À lire si tu veux comprendre **pourquoi** on choisit OpenClaude / Claude Code / Ollama plutôt qu'un simple chatbot.

### [agentic-ai-code-with-ia-v2](https://github.com/inskillflow/agentic-ai-code-with-ia-v2) *(privé)*

**Documentation ultra complète — référence interne.**
Version longue et exhaustive : tous les détails sur chaque outil, chaque flag, chaque option de chaque slash command, comparatifs lourds, benchmarks de modèles GGUF, cas d'usage pro.

Dépôt **privé** (accessible aux membres de l'organisation `inskillflow`). Si tu cherches l'information la plus complète et que tu y as accès, c'est là. Sinon, v3 (ce dépôt) est le résumé pédagogique public.

### [agentic-ai-code-with-ia-v3](https://github.com/inskillflow/agentic-ai-code-with-ia-v3) *(ce dépôt)*

**Résumé pédagogique de v2, mais plus structuré et plus complet sur le multiplateforme.**
Reprend l'essentiel de v2 sous forme de tutos courts, organisés par OS (Linux / macOS / Windows) et par parcours pédagogique, avec OpenRouter ajouté comme alternative cloud gratuite. Public, ouvert aux étudiants.

### [gemma-agressif-war-machine-2](https://github.com/inskillflow/gemma-agressif-war-machine-2)

**Modèle local 100 % offline avec llama.cpp + GPU NVIDIA.**
Setup complet pour faire tourner un LLM **uniquement en local** (zéro cloud, zéro API key, zéro dépendance internet une fois téléchargé) :

- compilation de `llama.cpp` avec **CUDA** pour exploiter à fond le GPU NVIDIA
- modèle Gemma agressif (variante non-censurée) prêt à l'emploi
- scripts de démarrage du serveur OpenAI-compatible
- intégration directe avec OpenClaude via `OPENAI_BASE_URL`

À utiliser quand tu veux la **performance maximale en local** (latence minimale, modèles non bridés, pas de quota), avec une carte NVIDIA correcte (≥ 8 Go VRAM recommandé).

---

### Quel dépôt choisir selon ton besoin

| Besoin | Dépôt à ouvrir |
|---|---|
| Comprendre les concepts de base et le pourquoi | **v1** |
| Apprendre vite avec des tutos clairs sur ton OS | **v3** *(ici)* |
| Référence exhaustive (membres `inskillflow`) | **v2** |
| Tourner un LLM 100 % local optimisé GPU NVIDIA | **gemma-agressif-war-machine-2** |
